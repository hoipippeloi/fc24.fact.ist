---
title: fc24.fact.ist / home
hide_title: true
---

<!--
Section: Init and header
-->

```recs
select * from csv.fc24_players
```

<h1>FC24 Statistics</h1>
<p>This app hosts analyses and insights to understand the <a class="link" href="https://www.ea.com/nl-nl/games/ea-sports-fc/fc-24" target="_blank">FC24</a> players and teams data better.</p>

<hr class="uk-margin uk-divider-icon" />

<!--
Section: High level stats
-->

```totals
select 
    count(*) as total_players,
    count(distinct nationality_name) as total_countries,
    count(distinct club_name) as total_clubs,
    avg(age) as average_age,
    avg(height_cm) as average_height,
    avg(weight_kg) as average_weight,
    avg(overall) as average_rating,
    avg(wage_eur) as average_wage,
    avg(potential) as average_potential,
    avg(value_eur) as average_value

from ${recs} recs
```

<Grid cols=5>
    <div>
        <BigValue
            title="Total Players"
            data={totals} 
            value=total_players
            fmt='#,##0'
        />
    </div>

    <div>
        <BigValue
            title="Total Countries"
            data={totals} 
            value=total_countries
            fmt='#,##0'
        />
    </div>

    <div>
        <BigValue
            title="Total Clubs"
            data={totals} 
            value=total_clubs
            fmt='#,##0'
        />
    </div>

    <div>
        <BigValue
            title="Average Age"
            data={totals} 
            value=average_age
            fmt='#,##0'
        />
    </div>

    <div>
        <BigValue
            title="Average Height in CM"
            data={totals} 
            value=average_height
            fmt='#,##0'
        />
    </div>

    <div>
        <BigValue
            title="Average Weight in KG"
            data={totals} 
            value=average_weight
            fmt='#,##0'
        />
    </div>

    <div>
        <BigValue
            title="Average Overall Rating"
            data={totals} 
            value=average_rating
            fmt='#,##0'
        />
    </div>

    <div>
        <BigValue
            title="Average Potential"
            data={totals} 
            value=average_potential
            fmt='#,##0'
        />
    </div>

    <div>
        <BigValue
            title="Average Wage in €"
            data={totals} 
            value=average_wage
            fmt='#,##0'
        />
    </div>

    <div>
        <BigValue
            title="Average Value in €"
            data={totals} 
            value=average_value
            fmt='#,##0'
        />
    </div>
</Grid>

<hr class="uk-margin uk-divider-icon" />

<!--
Section: Top 12 Players based on Rating
-->

```player_potential
select
    player_id as pid,
    'https://cdn.fifacm.com/content/media/imgs/fc24/players/p'|| player_id::INTEGER::VARCHAR ||'.png' as img,
    long_name,
    short_name,
    club_name,
    club_position,
    nationality_name,
    player_traits,
    avg(overall) as overall_rating,
    avg(potential) as potential_rating

from ${recs} recs
group by 1,2,3,4,5,6,7,8
order by 9 desc
limit 12
```

<h3>Top 12 Players based on Rating</h3>

<Grid cols=3>
    {#each player_potential as pp}
    <div>
        <div class="uk-card uk-card-default uk-card-body">
            <img class="uk-preserve-width uk-border-circle uk-align-right" style="border:1px solid #ccc;" src="{pp.img}" width="80" alt="">
            <span 
                class="uk-preserve-width uk-border-circle uk-position-small uk-position-top-right uk-text-center" 
                style="width:30px;height:30px;border:1px solid #ccc;padding:5px;">
                {pp.overall_rating}
            </span>
            <h3 class="uk-card-title">{pp.short_name}</h3>
            <!--<p><a class="link" href="/{pp.club_name}/">{pp.club_name}</a> / {pp.nationality_name}</p>-->
            <p>{pp.club_name} / {pp.nationality_name}</p>
        </div>
    </div>
    {/each}
</Grid>

<hr class="uk-margin uk-divider-icon">

<!--
Section: Top 20 Countries
-->

```by_country
select
    nationality_id,
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
group by 1,2
order by 3 desc
limit 20
```

<h3>Top 20 Countries</h3>

<Grid cols=2>
    <div>
        <BarChart
            title='Number of Players'
            data={by_country}
            x=nationality_name 
            y=total 
            swapXY=true
            colorPalette={
                [
                '#06871A'
                ]
            }
            renderer=svg
        />
    </div>

    <div>
        <BarChart
            title='Number of Clubs'
            data={by_country}
            x=nationality_name 
            y=total_clubs 
            swapXY=true
            colorPalette={
                [
                '#06871A'
                ]
            }
            renderer=svg
        />
    </div>
</Grid>

<hr class="uk-margin uk-divider-icon" />

<!--
Section: Top 20 Clubs - Skills
-->

```by_club
WITH RankedClubs AS (
    SELECT
        club.club_name,
        club.club_team_id,
        AVG(club.overall)::INTEGER AS overall,
        AVG(club.pace)::INTEGER as pace,
        AVG(club.shooting)::INTEGER as shooting,
        AVG(club.passing)::INTEGER as passing,
        AVG(club.dribbling)::INTEGER as dribbling,
        AVG(club.defending)::INTEGER as defending,
        AVG(club.physic)::INTEGER as physic,
        AVG(club.mentality_composure)::INTEGER as composure

    FROM ${recs} club
    GROUP BY club.club_name, club.club_team_id
    ORDER BY overall DESC
    LIMIT 20
)
SELECT
    'score' as ph,
    club_name,
    club_team_id,
    overall,
    1 - ((MAX(overall) OVER () - overall) / (MAX(overall) OVER () - MIN(overall) OVER ())) + 0.1 as rank_overall,
    pace,
    1 - ((MAX(pace) OVER () - pace) / (MAX(pace) OVER () - MIN(pace) OVER ())) + 0.1 as rank_pace,
    shooting,
    1 - ((MAX(shooting) OVER () - shooting) / (MAX(shooting) OVER () - MIN(shooting) OVER ())) + 0.1 as rank_shooting,
    passing,
    1 - ((MAX(passing) OVER () - passing) / (MAX(passing) OVER () - MIN(passing) OVER ())) + 0.1 as rank_passing,
    dribbling,
    1 - ((MAX(dribbling) OVER () - dribbling) / (MAX(dribbling) OVER () - MIN(dribbling) OVER ())) + 0.1 as rank_dribbling,
    defending,
    1 - ((MAX(defending) OVER () - defending) / (MAX(defending) OVER () - MIN(defending) OVER ())) + 0.1 as rank_defending,
    physic,
    1 - ((MAX(physic) OVER () - physic) / (MAX(physic) OVER () - MIN(physic) OVER ())) + 0.1 as rank_physic,
    composure,
    1 - ((MAX(composure) OVER () - composure) / (MAX(composure) OVER () - MIN(composure) OVER ())) + 0.1 as rank_composure

FROM RankedClubs
ORDER BY overall DESC
```

<h3>Top 20 Clubs - Skills</h3>

<table class="uk-table uk-table-small uk-table-divider">
    <thead>
        <tr>
            <th style="width:2%;text-align:center;"></th>
            <th>Club</th>
            <th style="width:8%;text-align:center;">Overall</th>
            <th style="width:8%;text-align:center;">Pace</th>
            <th style="width:8%;text-align:center;">Shooting</th>
            <th style="width:8%;text-align:center;">Passing</th>
            <th style="width:8%;text-align:center;">Dribbling</th>
            <th style="width:8%;text-align:center;">Defending</th>
        </tr>
    </thead>
    <tbody>
    {#each by_club as hm}
        <tr>
            <td style="text-align:center;">
            <img class="uk-preserve-width uk-border-circle" 
                src="https://fifastatic.fifaindex.com/FIFA24/teams/light/{hm.club_team_id}.png" 
            width="24" height="24" alt="">
            </td>
            <td>{hm.club_name}</td>
            <td style="text-align:center;background-color:rgba(0,163,20,{hm.rank_overall});">{hm.overall}</td>
            <td style="text-align:center;background-color:rgba(0,163,20,{hm.rank_pace});">{hm.pace}</td>
            <td style="text-align:center;background-color:rgba(0,163,20,{hm.rank_shooting});">{hm.shooting}</td>
            <td style="text-align:center;background-color:rgba(0,163,20,{hm.rank_passing});">{hm.passing}</td>
            <td style="text-align:center;background-color:rgba(0,163,20,{hm.rank_dribbling});">{hm.dribbling}</td>
            <td style="text-align:center;background-color:rgba(0,163,20,{hm.rank_defending});">{hm.defending}</td>
        </tr>
    {/each}
    </tbody>
</table>