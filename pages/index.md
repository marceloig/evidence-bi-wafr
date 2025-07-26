---
title: Overview
description: Overview of the pillars in AWS Well-Architected Framework Review.
---
<Grid cols=3>
<Link 
    url="pillars/operationalExcellence"
    label="Operational Excellence"
/>
<Link 
    url="pillars/security"
    label="Security"
/>
<Link 
    url="pillars/reliability"
    label="Reliability"
/>
<Link 
    url="pillars/performance"
    label="Performance Efficiency"
/>
<Link 
    url="pillars/costOptimization"
    label="Cost Optimization"
/>
<Link 
    url="pillars/sustainability"
    label="Sustainability"
/>
</Grid>

```sql workload
select 
    w.workload_id,
    w.workload_name
from steampipe.aws_wellarchitected_answer as a
inner join steampipe.aws_wellarchitected_workload as w
on a.workload_id = w.workload_id
where a.lens_alias = 'wellarchitected'

```

<Dropdown
    name=workload_id
    data={workload}
    value=workload_id
    label=workload_name
/>

```sql questions_overview_risk
select 
  case a.risk
      when 'HIGH'
      then 'High Risk'
      when 'MEDIUM'
      then 'Medium Risk'
      when 'NONE'
      then 'Resolved'
      when 'NOT_APPLICABLE'
      then 'Not Applicable'
      when 'UNANSWERED'
      then 'Unanswered' else 'Others status'
  end as name, 
  count(*) as value
from steampipe.aws_wellarchitected_answer as a
inner join steampipe.aws_wellarchitected_workload as w
on a.workload_id = w.workload_id
where a.lens_alias = 'wellarchitected'
and a.workload_id = '${inputs.workload_id.value}'
group by a.risk
order by a.risk
```

<ECharts config={
    {
        tooltip: {
            formatter: '{b}: {c} ({d}%)'
        },
      series: [
        {
          type: 'pie',
          radius: ['40%', '70%'],
          data: [...questions_overview_risk],
        }
      ]
      }
    }
/>

```sql questions_overview_remediation
select
  m.recorded_at,
  case a.risk
    when 'HIGH'
    then 'High Risk'
    when 'MEDIUM'
    then 'Medium Risk'
    when 'NONE'
    then 'Resolved'
    when 'NOT_APPLICABLE'
    then 'Not Applicable'
    when 'UNANSWERED'
    then 'Unanswered' else 'Others status'
  end as risk_description,
  count(*) as value
from
  steampipe.aws_wellarchitected_milestone as m
left join steampipe.aws_wellarchitected_workload as w
on m.workload_id = w.workload_id
left join steampipe.aws_wellarchitected_answer as a
on a.workload_id = w.workload_id
WHERE a.workload_id = '${inputs.workload_id.value}'
group by m.recorded_at, a.risk
order by a.risk
```

<BarChart 
    data={questions_overview_remediation}
    x=recorded_at
    y=value
    series=risk_description
/>

```sql risk_description
select
  a.risk,
  case a.risk
    when 'HIGH'
    then 'High Risk'
    when 'MEDIUM'
    then 'Medium Risk'
    when 'NONE'
    then 'Resolved'
    when 'NOT_APPLICABLE'
    then 'Not Applicable'
    when 'UNANSWERED'
    then 'Unanswered' else 'Others status'
  end as risk_description
from steampipe.aws_wellarchitected_answer as a
group by a.risk
```

```sql base_action_plan
WITH choices_all AS (
    SELECT
        a.workload_id,
        a.question_id,
        json_extract_string(choice_obj, 'AdditionalResources') AS additional_resources,
        json_extract_string(choice_obj, 'ChoiceId') AS choice_id,
        json_extract_string(choice_obj, 'Description') AS description,
        json_extract_string(choice_obj, 'HelpfulResource') AS HelpfulResource,
        json_extract_string(choice_obj, 'ImprovementPlan') AS ImprovementPlan,
        json_extract_string(choice_obj, 'Title') AS title
    FROM steampipe.aws_wellarchitected_answer AS a
    CROSS JOIN UNNEST(json_extract(a.choices, '$[*]')) AS t(choice_obj)
    WHERE a.lens_alias = 'wellarchitected'
      AND a.workload_id = '${inputs.workload_id.value}'
)
SELECT a.question_title,
       cti.description,
       cti.title AS best_practice,
       aqc.risk AS best_practice_risk,
       aqc.Complexity,
       aqc.Duration,
       aqc.Improvement_Plan
FROM steampipe.aws_wellarchitected_answer AS a
INNER JOIN choices_all AS cti ON a.workload_id = cti.workload_id AND a.question_id = cti.question_id
LEFT JOIN csv.aws_wellarchitected_question_choices AS aqc ON cti.choice_id = aqc.choice_id
INNER JOIN steampipe.aws_wellarchitected_workload AS w ON a.workload_id = w.workload_id
WHERE lens_alias = 'wellarchitected'
  AND cti.title <> 'None of these'
  AND a.workload_id = '${inputs.workload_id.value}'
  AND a.risk = '${inputs.risk.value}'
```
<Dropdown
    name=risk
    data={risk_description}
    value=risk
    label=risk_description
    defaultValue="HIGH"
/>

<DataTable data={base_action_plan} search=true wrapTitles=true rowShading=true groupBy=question_title groupsOpen=false>
<Column id=question_title />
<Column id=description />
<Column id=best_practice />
<Column id=best_practice_risk />
<Column id=Complexity />
<Column id=Duration />
<Column id=Improvement_Plan contentType=link linkLabel=Improvement_Plan openInNewTab=true />
</DataTable>