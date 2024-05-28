---
title: fc24.fact.ist / metrics
hide_title: true
---


```recs
select 
    *,
    'https://cdn.fifacm.com/content/media/imgs/fc24/players/p'|| player_id::INTEGER::VARCHAR ||'.png' as player_img

from csv.fc24_players
```

```by_player
select
    short_name,
    player_img, 
    player_id,
    nationality_name,
    count(*) as total,
    count(distinct club_name) as total_clubs,
    avg(age) as average_age,
    avg(height_cm) as average_height,
    avg(weight_kg) as average_weight,
    avg(overall) as average_rating,
    avg(wage_eur) as average_wage,
    avg(potential) as average_potential,
    avg(value_eur) as average_value
    
from ${recs} recs
group by 1,2,3,4
limit 500
```

```by_country
with stats as (
select
    nationality_id,
    nationality_name,
    avg(height_cm) as average_height,
    avg(overall) as average_rating,
    avg(pace) as average_pace,
    avg(potential) as average_potential,
    avg(value_eur) as average_value
    
from ${recs} recs
group by 1,2
)

select * from stats
limit 20
```

<h1>FC24 Metrics</h1>

<legend>Select a metric</legend>
<ButtonGroup 
    name=metric_select
>
    <ButtonGroupItem valueLabel="Avg Potential" value="average_potential" />
    <ButtonGroupItem valueLabel="Avg Value" value="average_value" default />
    <ButtonGroupItem valueLabel="Avg Height in cm" value="average_height" />
</ButtonGroup>


<Grid cols=2 gapSize=lg>

    <div>
        <h3>Top 20 Countries</h3>
        <div style="padding-right:60px;">
            <BarChart
                data={by_country}
                x=nationality_name 
                y='{inputs.metric_select}'
                swapXY=true
                colorPalette={
                    [
                    '#777'
                    ]
                }
                renderer=svg
                chartAreaHeight=180
            />
        </div>
    </div>

    <div>
        <h3>Distribution of Players</h3>
        <div uk-tooltip="title: Hover over a dot for more info; pos: top-right">
            <ScatterPlot 
                data={by_player}
                tooltipTitle=short_name
                x='{inputs.metric_select}' 
                y=average_rating
                series=nationality_name
                xAxisTitle=true 
                yAxisTitle=true
                chartAreaHeight=440
            />
        </div>
    </div>

</Grid>

<style>
legend {
    font-size:0.9em;
    color:#888;
    font-weight:700;
}
</style>