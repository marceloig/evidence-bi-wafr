---
title: Well-Architected Framework Review Report - Benchmark
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
          text: 'Total Risk - Operacional Excellence',
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
        legend: {
          show: true,
          bottom: 'left',
        },
        tooltip: {
            formatter: '{b}: {c}'
        },
        series: [
          {
            type: 'pie',
            radius: ['25%', '50%'],  // Smaller pie radius
            center: ['50%', '50%'],
            data: [...questions_overview_operacionalExcellence],
            label: {
              formatter: '{d}%',  // Show only the value
            }
          }
        ]
      }
    }
  />
  <ECharts config={
      {
        title: {
          text: 'Total Risk - Security',
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
        legend: {
          show: true,
          bottom: 'left',
        },
        tooltip: {
            formatter: '{b}: {c}'
        },
        series: [
          {
            type: 'pie',
            radius: ['25%', '50%'],  // Smaller pie radius
            center: ['50%', '50%'],
            data: [...questions_overview_security],
            label: {
              formatter: '{d}%'  // Show only the value
            }
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
          text: 'Total Risk - Reliability',
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
        legend: {
          show: true,
          bottom: 'left',
        },
        tooltip: {
            formatter: '{b}: {c}'
        },
        series: [
          {
            type: 'pie',
            radius: ['25%', '50%'],  // Smaller pie radius
            center: ['50%', '50%'],
            data: [...questions_overview_reliability],
            label: {
              formatter: '{d}%'  // Show only the value
            }
          }
        ]
      }
    }
  />
  <ECharts config={
      {
        title: {
          text: 'Total Risk - Performance Efficiency',
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
        legend: {
          show: true,
          bottom: 'left',
        },
        tooltip: {
            formatter: '{b}: {c}'
        },
        series: [
          {
            type: 'pie',
            radius: ['25%', '50%'],  // Smaller pie radius
            center: ['50%', '50%'],
            data: [...questions_overview_performance],
            label: {
              formatter: '{d}%'  // Show only the value
            }
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
WITH default_risks AS (
  SELECT * FROM (VALUES 
    ('High Risk', 0),
    ('Medium Risk', 0),
    ('Resolved', 0)
  ) AS t(name, value)
),
actual_counts AS (
  select risk as name, count(*) as value 
  from ${questions_overview_pillar_id}
  where pillar_id = 'sustainability' 
  group by risk
)
SELECT 
  dr.name,
  COALESCE(ac.value, dr.value) as value
FROM default_risks dr
LEFT JOIN actual_counts ac ON dr.name = ac.name
ORDER BY 
  CASE dr.name
    WHEN 'High Risk' THEN 1
    WHEN 'Medium Risk' THEN 2
    WHEN 'Resolved' THEN 3
    ELSE 4
  END

```

<Grid cols=2>
  <ECharts config={
      {
        title: {
          text: 'Total Risk - Cost Optimization',
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
        legend: {
          show: true,
          bottom: 'left',
        },
        tooltip: {
            formatter: '{b}: {c}'
        },
        series: [
          {
            type: 'pie',
            radius: ['25%', '50%'],  // Smaller pie radius
            center: ['50%', '50%'],
            data: [...questions_overview_costOptimization],
            label: {
              formatter: '{d}%'  // Show only the value
            }
          }
        ]
      }
    }
  />
  <ECharts config={
      {
        title: {
          text: 'Total Risk - Sustainability',
          left: 'left'
        },
        legend: {
          show: true,
          bottom: 'left',
        },
        tooltip: {
            formatter: '{b}: {c} ({d}%)'
        },
        series: [
          {
            type: 'pie',
            radius: ['25%', '50%'],  // Smaller pie radius
            center: ['50%', '50%'],
            data: [...questions_overview_sustainability],
            label: {
              formatter: '{d}%'  // Show only the value
            }
          }
        ]
      }
    }
  />
</Grid>
