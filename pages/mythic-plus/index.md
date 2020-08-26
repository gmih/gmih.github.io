---
layout: page
title: Top Guild Mythic+
weight: 1
nav: yes
permalink: mythic-plus/
---

<!-- Organizations -->
{% assign leaderboard = site.data.leaderboard %}

<!-- Document -->

{% for leaderboardRow in leaderboard %}
    <ul>
        <li>{{ leaderboardRow }}</li>
    </ul>
{% endfor %}