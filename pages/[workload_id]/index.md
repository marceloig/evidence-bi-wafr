---
title: WAFR - Overview
description: Overview of AWS Well-Architected Framework Review.
---

```sql workload
select 
    w.workload_id,
    w.workload_name
from camarim.aws_wellarchitected_answer as a
inner join camarim.aws_wellarchitected_workload as w
on a.workload_id = w.workload_id
where a.lens_alias = 'wellarchitected'
and a.workload_id = '${params.workload_id}'

```

<Grid cols=3>
<BigLink url='operationalExcellence'>Operational Excellence</BigLink>
<BigLink url='security'>Security</BigLink>
<BigLink url='reliability'>Reliability</BigLink>
<BigLink url='performance'>Performance Efficiency</BigLink>
<BigLink url='costOptimization'>Cost Optimization</BigLink>
<BigLink url='sustainability'>Sustainability</BigLink>
</Grid>

# <Value data={workload} column=workload_name />

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
from camarim.aws_wellarchitected_answer as a
inner join camarim.aws_wellarchitected_workload as w
on a.workload_id = w.workload_id
where a.lens_alias = 'wellarchitected'
and a.workload_id = '${params.workload_id}'
group by a.risk
order by a.risk
```

<ECharts config={
    {
      title: {
        text: 'Total Risk',
        left: 'left'
      },
      toolbox: {
        show: true,
        feature: {
          saveAsImage: {
            show: true,
            title: 'Save as Image',
            type: 'png',  // 'png', 'jpeg', 'svg'
            name: 'pie-chart',  // filename
          }
        }
      },
      tooltip: {
          formatter: '{b}: {c}'
      },
      series: [
        {
          type: 'pie',
          radius: ['40%', '70%'],
          data: [...questions_overview_risk],
          label: {
            formatter: '{b}: ({d}%)'  // Show only the value
          }
        }
      ]
      }
    }
/>

```sql questions_overview_remediation
select
  date_trunc('day', m.recorded_at) as recorded_at,
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
  count(*) as total
from
  camarim.aws_wellarchitected_milestone as m
left join camarim.aws_wellarchitected_workload as w
on m.workload_id = w.workload_id
left join camarim.aws_wellarchitected_answer as a
on a.workload_id = w.workload_id
WHERE a.workload_id = '${params.workload_id}'
group by m.recorded_at, a.risk
order by m.recorded_at, a.risk
```

<BarChart
    title="Remediations Over Time"
    data={questions_overview_remediation}
    x=recorded_at
    y=total
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
from camarim.aws_wellarchitected_answer as a
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
    FROM camarim.aws_wellarchitected_answer AS a
    CROSS JOIN UNNEST(json_extract(a.choices, '$[*]')) AS t(choice_obj)
    WHERE a.lens_alias = 'wellarchitected'
      AND a.workload_id = '${params.workload_id}'
)
SELECT a.question_title,
       cti.description,
       cti.title AS best_practice,
       aqc.risk AS best_practice_risk,
       aqc.Complexity,
       aqc.Duration,
       aqc.Improvement_Plan
FROM camarim.aws_wellarchitected_answer AS a
INNER JOIN choices_all AS cti ON a.workload_id = cti.workload_id AND a.question_id = cti.question_id
LEFT JOIN csv.aws_wellarchitected_question_choices AS aqc ON cti.choice_id = aqc.choice_id
INNER JOIN camarim.aws_wellarchitected_workload AS w ON a.workload_id = w.workload_id
WHERE lens_alias = 'wellarchitected'
  AND cti.title <> 'None of these'
  AND a.workload_id = '${params.workload_id}'
  AND a.risk = '${inputs.risk.value}'
```
<LineBreak/>

<Dropdown
    name=risk
    data={risk_description}
    value=risk
    label=risk_description
    defaultValue="HIGH"
/>

<DataTable title="Base Action Plan" data={base_action_plan} search=true wrapTitles=true rowShading=true groupBy=question_title groupsOpen=false>
<Column id=question_title />
<Column id=description />
<Column id=best_practice />
<Column id=best_practice_risk />
<Column id=Complexity />
<Column id=Duration />
<Column id=Improvement_Plan contentType=link linkLabel=Improvement_Plan openInNewTab=true />
</DataTable>