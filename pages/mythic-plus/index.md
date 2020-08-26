---
layout: page
title: Top Guild Mythic+
weight: 1
nav: yes
permalink: mythic-plus/
---

<script>
fetch('/data/leaderboard.json')
  .then(response => response.json())
  .then(data => console.log(data));

</script>