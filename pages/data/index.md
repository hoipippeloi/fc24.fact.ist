---
title: fc24.fact.ist / club
hide_title: true
---

<script>
  //let by_club = []; // Assuming this will be populated with your data

  let maxOverall = 0;
  let maxPace = 0;

  // Function to calculate the maximum values for overall and pace
  function calculateMaxValues() {
    by_club.forEach(club => {
      if (club.overall > maxOverall) maxOverall = club.overall;
      if (club.pace > maxPace) maxPace = club.pace;
    });
  }

  // Function to calculate color based on value and max value
  function getColor(value, maxValue) {
    const startColor = 211; // Light-grey
    const ratio = value / maxValue;
    const colorValue = Math.round(startColor * (1 - ratio));
    return `rgb(${colorValue}, ${colorValue}, ${colorColor})`;
  }

  onMount(() => {
    calculateMaxValues();
  });
</script>

```raw
select * from csv.fc24_players
```

```recs
select * from csv.fc24_players
limit 10
```

<DataTable data={recs}/>

```totals
select
    avg(overall)::integer as overall,
    avg(pace)::integer as pace,
    avg(pace-25)::integer as min_pace,
    avg(pace+25)::integer as max_pace,
    avg(shooting)::integer as shooting,
    avg(passing)::integer as passing,
    avg(dribbling)::integer as dribbling,
    avg(defending)::integer as defending,
    avg(physic)::integer as physic,
    avg(mentality_composure)::integer as composure
    
from ${raw} raw
```


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

    FROM ${raw} club
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

<!--
{JSON.stringify(by_club)}
-->
<DataTable data={by_club}/>

<table class="uk-table uk-table-small uk-table-divider">
    <thead>
        <tr>
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
