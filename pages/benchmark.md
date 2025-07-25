---
title: Benchmark
---

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
          data: [...questions_overview_risk]
        }
      ]
      }
    }
/>

```sql questions_overview
select
	case
		a.pillar_id
		when 'reliability' then 'Reliability'
		when 'operationalExcellence' then 'Operational Excellence'
		when 'security' then 'Security'
		when 'costOptimization' then 'Cost Optimization'
		when 'sustainability' then 'Sustainability'
		when 'performance' then 'Performance Efficiency' else 'Others pillars'
	end as pillar_title,
  a.question_title,
	case
		a.risk
		when 'HIGH' then 'High Risk'
		when 'MEDIUM' then 'Medium Risk'
		when 'NONE' then 'Resolved'
		when 'NOT_APPLICABLE' then 'Not Applicable'
		when 'UNANSWERED' then 'Unanswered' else 'Others status'
	end as risk_description
from steampipe.aws_wellarchitected_answer as a
where a.lens_alias = 'wellarchitected'
```
<DataTable data={questions_overview} search=true/>

```sql questions_overview_pillar_id
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
  end as risk, 
  a.pillar_id
from steampipe.aws_wellarchitected_answer as a
where a.lens_alias = 'wellarchitected'
order by a.risk
```

```sql questions_overview_operacionalExcellence
select 
  risk as name, 
  count(*) as value
from ${questions_overview_pillar_id}
where pillar_id = 'operationalExcellence'
group by risk
```

```sql questions_overview_security
select 
  risk as name, 
  count(*) as value
from ${questions_overview_pillar_id}
where pillar_id = 'security'
group by risk
```

<Grid cols=2>
  <ECharts config={
      {
        title: {
          text: 'Operacional Excellence',
          left: 'center'
        },
        tooltip: {
            formatter: '{b}: {c} ({d}%)'
        },
        series: [
          {
            type: 'pie',
            radius: ['40%', '70%'],
            data: [...questions_overview_operacionalExcellence],
          }
        ]
      }
    }
  />
  <ECharts config={
      {
        title: {
          text: 'Security',
          left: 'center'
        },
        tooltip: {
            formatter: '{b}: {c} ({d}%)'
        },
        series: [
          {
            type: 'pie',
            radius: ['40%', '70%'],
            data: [...questions_overview_security],
          }
        ]
      }
    }
  />
</Grid>

```sql questions_overview_reliability
select 
  risk as name, 
  count(*) as value
from ${questions_overview_pillar_id}
where pillar_id = 'reliability'
group by risk
```

```sql questions_overview_performance
select 
  risk as name, 
  count(*) as value
from ${questions_overview_pillar_id}
where pillar_id = 'performance'
group by risk
```

<Grid cols=2>
  <ECharts config={
      {
        title: {
          text: 'Reliability',
          left: 'center'
        },
        tooltip: {
            formatter: '{b}: {c} ({d}%)'
        },
        series: [
          {
            type: 'pie',
            radius: ['40%', '70%'],
            data: [...questions_overview_reliability],
          }
        ]
      }
    }
  />
  <ECharts config={
      {
        title: {
          text: 'Performance Efficiency',
          left: 'center'
        },
        tooltip: {
            formatter: '{b}: {c} ({d}%)'
        },
        series: [
          {
            type: 'pie',
            radius: ['40%', '70%'],
            data: [...questions_overview_performance],
          }
        ]
      }
    }
  />
</Grid>

```sql questions_overview_costOptimization
select 
  risk as name, 
  count(*) as value
from ${questions_overview_pillar_id}
where pillar_id = 'costOptimization'
group by risk
```

```sql questions_overview_sustainability
select 
  risk as name, 
  count(*) as value
from ${questions_overview_pillar_id}
where pillar_id = 'sustainability'
group by risk

union all

select 
  'High Risk' as name,
  0 as value

order by name

```

<Grid cols=2>
  <ECharts config={
      {
        title: {
          text: 'Cost Optimization',
          left: 'center'
        },
        tooltip: {
            formatter: '{b}: {c} ({d}%)'
        },
        series: [
          {
            type: 'pie',
            radius: ['40%', '70%'],
            data: [...questions_overview_costOptimization],
          }
        ]
      }
    }
  />
  <ECharts config={
      {
        title: {
          text: 'Sustainability',
          left: 'center'
        },
        tooltip: {
            formatter: '{b}: {c} ({d}%)'
        },
        series: [
          {
            type: 'pie',
            radius: ['40%', '70%'],
            data: [...questions_overview_sustainability],
          }
        ]
      }
    }
  />
</Grid>
