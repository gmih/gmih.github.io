---
layout: page
title: Guild Mythic+ Leaderboard
weight: 1
nav: yes
permalink: mythic-plus/
---
<p class="leaderboard-description">Team up with at least 2 other guild members to qualify for this leaderboard. Resets every Mythic+ season.</p>
<ol id="gmih-leaderboard"></ol>
<script>
  var dungeonInfo = {
    "Mists of Tirna Scithe": {
      "1-star": 1800000,
      "2-stars": 1440000,
      "3-stars": 1080000,
      safeName: 'mists-of-tirna-scithe'
    },
    "The Necrotic Wake": {
      "1-star": 2160000,
      "2-stars": 1728000,
      "3-stars": 1296000,
      safeName: 'the-necrotic-wake'
    },
    "De Other Side": {
      "1-star": 2340000,
      "2-stars": 1872000,
      "3-stars": 1404000,
      safeName: 'de-other-side'
    },
    "Halls of Atonement": {
      "1-star": 1860000,
      "2-stars": 1488000,
      "3-stars": 1116000,
      safeName: 'halls-of-atonement'
    },
    "Plaguefall": {
      "1-star": 2280000,
      "2-stars": 1824000,
      "3-stars": 1368000,
      safeName: 'plaguefall'
    },
    "Sanguine Depths": {
      "1-star": 2460000,
      "2-stars": 1968000,
      "3-stars": 1476000,
      safeName: 'sanguine-depths'
    },
    "Spires of Ascension": {
      "1-star": 2340000,
      "2-stars": 1872000,
      "3-stars": 1404000,
      safeName: 'spires-of-ascension'
    },
    "Theater of Pain": {
      "1-star": 2220000,
      "2-stars": 1776000,
      "3-stars": 1332000,
      safeName: 'theater-of-pain'
    },
    "Tazavesh: Streets of Wonder": {
      "1-star": 2280000,
      "2-stars": 1824000,
      "3-stars": 1368000,
      safeName: 'streets-of-wonder'
    },
    "Tazavesh: So'leah's Gambit": {
      "1-star": 1800000,
      "2-stars": 1440000,
      "3-stars": 1080000,
      safeName: 'soleahs-gambit'
    }
  }

  function convertMS(milliseconds) {
    var day, hour, minute, seconds, over;
    
    if (milliseconds < 0) {
      milliseconds = Math.abs(milliseconds);
      over = true;
    }

    seconds = Math.floor(milliseconds / 1000);
    minute = Math.floor(seconds / 60);
    seconds = seconds % 60;
    hour = Math.floor(minute / 60);
    minute = minute % 60;
    day = Math.floor(hour / 24);
    hour = hour % 24;
    
    return `${hour}:${minute < 10 ? '0' : ''}${minute}:${seconds < 10 ? '0' : ''}${seconds}`;
}



function convertMSTooltip(milliseconds) {
    var day, hour, minute, seconds, over;
    
    if (milliseconds < 0) {
      milliseconds = Math.abs(milliseconds);
      over = true;
    }

    seconds = Math.floor(milliseconds / 1000);
    minute = Math.floor(seconds / 60);
    seconds = seconds % 60;
    hour = Math.floor(minute / 60);
    minute = minute % 60;
    day = Math.floor(hour / 24);
    hour = hour % 24;
    
    return `${over ? 'Over by ' : 'Under by '}${hour}:${minute < 10 ? '0' : ''}${minute}:${seconds < 10 ? '0' : ''}${seconds}`;
}

function timeDiff(curr, prev) {
  var ms_Min = 60 * 1000; // milliseconds in Minute 
  var ms_Hour = ms_Min * 60; // milliseconds in Hour 
  var ms_Day = ms_Hour * 24; // milliseconds in day 
  var ms_Mon = ms_Day * 30; // milliseconds in Month 
  var ms_Yr = ms_Day * 365; // milliseconds in Year 
  var diff = curr - prev; //difference between dates. 
  // If the diff is less then milliseconds in a minute 
  if (diff < ms_Min) {
      return Math.round(diff / 1000) + ' seconds ago'; 

      // If the diff is less then milliseconds in a Hour 
  } else if (diff < ms_Hour) { 
      if (Math.round(diff / ms_Min) === 1) {
        return Math.round(diff / ms_Min) + ' minute ago'; 
      }
      return Math.round(diff / ms_Min) + ' minutes ago'; 

      // If the diff is less then milliseconds in a day 
  } else if (diff < ms_Day) { 
      if (Math.round(diff / ms_Hour) === 1) {
        return Math.round(diff / ms_Hour) + ' hour ago'; 
      }
      return Math.round(diff / ms_Hour) + ' hours ago'; 

      // If the diff is less then milliseconds in a Month 
  } else  if (diff < ms_Mon) { 
      if (Math.round(diff / ms_Day) === 1) {
        return Math.round(diff / ms_Day) + ' day ago'; 
      }
      return Math.round(diff / ms_Day) + ' days ago';
  } else if (diff < ms_Yr) {
      if (Math.round(diff / ms_Mon) === 1) {
        return Math.round(diff / ms_Mon) + ' month ago'; 
      }
      return Math.round(diff / ms_Mon) + ' months ago'; 
  } else { 
    if (Math.round(diff / ms_Yr) === 1) {
      return Math.round(diff / ms_Yr) + ' year ago';
    }
      return Math.round(diff / ms_Yr) + ' years ago'; 
  } 
} 

  function createLeaderboard(data) {
    var tankSpecs = ["Protection", "Vengeance", "Brewmaster", "Guardian", "Blood"];
    var healerSpecs = ["Restoration", "Mistweaver", "Holy", "Discipline"];


    data.sort(function (data1, data2) {
      // Sort by Key Difficulty
      if (parseInt(data1.Key) < parseInt(data2.Key)) return 1;
      if (parseInt(data1.Key) > parseInt(data2.Key)) return -1;

      // Sort by duration
      if (dungeonInfo[data1.Dungeon]["1-star"] - parseInt(data1.Duration) > dungeonInfo[data2.Dungeon]["1-star"] - parseInt(data2.Duration)) return -1;
      if (dungeonInfo[data1.Dungeon]["1-star"] - parseInt(data1.Duration) < dungeonInfo[data2.Dungeon]["1-star"] - parseInt(data2.Duration)) return 1;

    }).forEach(function(guildRun) {
      var stars = "";
      var group = {
        tank: {
        },
        healer: {
        },
        dps: []
      };
      var groupMembers = [[guildRun.Member1, guildRun["Member1-Spec"], guildRun["Member1-Guild"]], [guildRun.Member2, guildRun["Member2-Spec"], guildRun["Member2-Guild"]], [guildRun.Member3, guildRun["Member3-Spec"], guildRun["Member3-Guild"]], [guildRun.Member4, guildRun["Member4-Spec"], guildRun["Member4-Guild"]], [guildRun.Member5, guildRun["Member5-Spec"], guildRun["Member5-Guild"]]];
      groupMembers.forEach(function(member) {
        if (tankSpecs.includes(member[1])) {
          group.tank = {
            name: decodeURIComponent(member[0]),
            member: member[2]
          };
        } else if (healerSpecs.includes(member[1])) {
          group.healer = {
            name: decodeURIComponent(member[0]),
            member: member[2]
          }
        } else {
          group.dps.push({
            name:decodeURIComponent(member[0]),
            member: member[2]
          });
        }
      });
      
      if(guildRun.Duration <= dungeonInfo[guildRun.Dungeon]["1-star"]) {
        if(guildRun.Duration <= dungeonInfo[guildRun.Dungeon]["3-star"]) {
          stars = "three-star";
        } else if(guildRun.Duration <= dungeonInfo[guildRun.Dungeon]["2-star"]) {
          stars = "two-star";
        } else {
          stars = "one-star";
        }
      }


      var run = document.createElement('li');
      run.classList.add('guild-run', `guild-run--${dungeonInfo[guildRun.Dungeon].safeName}`);
      var runHTML = `<div class="guild-run__container"><div class="dungeon-details">
        <h2 class="dungeon-name"><span class="dungeon-level">+${guildRun.Key}</span> ${guildRun.Dungeon}</h2>
        <div class="dungeon-details__container">
          <div class="timer">
            <div class="timer__time"><svg fill="#000000" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" x="0px" y="0px" viewBox="0 0 100 100" style="enable-background:new 0 0 100 100;" xml:space="preserve"><style type="text/css">.st0{fill:none;}</style><rect class="st0" width="100" height="100"></rect><path d="M74.8,30.5c-0.3-0.3-0.7-0.7-1.1-1l1-1.3l0.6,0.5c0.6,0.4,1.4,0.3,1.8-0.2l1.8-2.3c0.4-0.6,0.3-1.4-0.2-1.8l-3.6-2.8  l-3.6-2.8c-0.6-0.4-1.4-0.3-1.8,0.2L68,21.1c-0.4,0.6-0.3,1.4,0.2,1.8l0.6,0.5l-1.2,1.5c-3.6-2.1-7.7-3.6-11.8-4.2v-2.9h1.1  c1.1,0,2-0.9,2-2v-4.1c0-1.1-0.9-2-2-2h-4h-9.6c-1.1,0-2,0.9-2,2v4.1c0,1.1,0.9,2,2,2h1.1v2.9c-4.2,0.7-8.2,2.1-11.8,4.2l-1.2-1.5  l0.6-0.5c0.6-0.4,0.7-1.3,0.2-1.8l-1.8-2.3c-0.4-0.6-1.3-0.7-1.8-0.2l-3.6,2.8l-3.6,2.8c-0.6,0.4-0.7,1.3-0.2,1.8l1.8,2.3  c0.4,0.6,1.3,0.7,1.8,0.2l0.6-0.5l1,1.3c-0.4,0.3-0.7,0.7-1.1,1c-6.6,6.6-10.3,15.4-10.3,24.8s3.6,18.2,10.3,24.8  c6.6,6.6,15.4,10.3,24.8,10.3s18.2-3.6,24.8-10.3c6.6-6.6,10.3-15.4,10.3-24.8S81.4,37.1,74.8,30.5z M50,85  c-16.4,0-29.8-13.4-29.8-29.8c0-16.4,13.4-29.8,29.8-29.8s29.8,13.4,29.8,29.8C79.8,71.7,66.4,85,50,85z"></path><path d="M50,30.5v24.8h24.8C74.8,41.6,63.7,30.5,50,30.5z"></path></svg>${convertMS(guildRun.Duration)}<span class="duration-tooltip">${convertMSTooltip(dungeonInfo[guildRun.Dungeon]["1-star"] - guildRun.Duration)}</span></div>
            <div class="timer__stars ${stars}">
              <svg class="svg-inline--fa fa-star fa-w-18" aria-hidden="true" data-prefix="fas" data-icon="star" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 576 512" data-fa-i2svg=""><path fill="" d="M259.3 17.8L194 150.2 47.9 171.5c-26.2 3.8-36.7 36.1-17.7 54.6l105.7 103-25 145.5c-4.5 26.3 23.2 46 46.4 33.7L288 439.6l130.7 68.7c23.2 12.2 50.9-7.4 46.4-33.7l-25-145.5 105.7-103c19-18.5 8.5-50.8-17.7-54.6L382 150.2 316.7 17.8c-11.7-23.6-45.6-23.9-57.4 0z"></path></svg>
              <svg class="svg-inline--fa fa-star fa-w-18" aria-hidden="true" data-prefix="fas" data-icon="star" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 576 512" data-fa-i2svg=""><path fill="" d="M259.3 17.8L194 150.2 47.9 171.5c-26.2 3.8-36.7 36.1-17.7 54.6l105.7 103-25 145.5c-4.5 26.3 23.2 46 46.4 33.7L288 439.6l130.7 68.7c23.2 12.2 50.9-7.4 46.4-33.7l-25-145.5 105.7-103c19-18.5 8.5-50.8-17.7-54.6L382 150.2 316.7 17.8c-11.7-23.6-45.6-23.9-57.4 0z"></path></svg>
              <svg class="svg-inline--fa fa-star fa-w-18" aria-hidden="true" data-prefix="fas" data-icon="star" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 576 512" data-fa-i2svg=""><path fill="" d="M259.3 17.8L194 150.2 47.9 171.5c-26.2 3.8-36.7 36.1-17.7 54.6l105.7 103-25 145.5c-4.5 26.3 23.2 46 46.4 33.7L288 439.6l130.7 68.7c23.2 12.2 50.9-7.4 46.4-33.7l-25-145.5 105.7-103c19-18.5 8.5-50.8-17.7-54.6L382 150.2 316.7 17.8c-11.7-23.6-45.6-23.9-57.4 0z"></path></svg>
            </div>
          </div>
          <ul class="affixes">
            ${guildRun.Affix1 ? `<li class="${guildRun.Affix1.toLowerCase()}"><span>${guildRun.Affix1}</span></li>`: ''}
            ${guildRun.Affix2 ? `<li class="${guildRun.Affix2.toLowerCase()}"><span>${guildRun.Affix2}</span></li>`: ''}
            ${guildRun.Affix3 ? `<li class="${guildRun.Affix3.toLowerCase()}"><span>${guildRun.Affix3}</span></li>`: ''}
            ${guildRun.Affix4 ? `<li class="${guildRun.Affix4.toLowerCase()}"><span>${guildRun.Affix4}</span></li>`: ''}
          </ul>
          <div class="date-information__container"><svg xmlns="http://www.w3.org/2000/svg" data-name="Layer 1" viewBox="0 0 64 64" x="0px" y="0px"><title>Artboard 43</title><path d="M58.4567,7.6694H50.1889v3.5432a4.9606,4.9606,0,1,1-9.9212,0V7.6694H23.7322v3.5432a4.9606,4.9606,0,1,1-9.9211,0V7.6694H5.5433A3.5446,3.5446,0,0,0,2,11.2126V58.4568A3.5474,3.5474,0,0,0,5.5433,62H58.4567A3.5474,3.5474,0,0,0,62,58.4568V11.2126A3.5446,3.5446,0,0,0,58.4567,7.6694Zm2.126,50.7874a2.1285,2.1285,0,0,1-2.126,2.1258H5.5433a2.1285,2.1285,0,0,1-2.126-2.1258V22.3148H60.5827ZM15.2284,11.2126V5.5434a3.5433,3.5433,0,0,1,7.0866,0v5.6692a3.5433,3.5433,0,1,1-7.0866,0Zm26.4566,0V5.5434a3.5433,3.5433,0,1,1,7.0866,0v5.6692a3.5433,3.5433,0,1,1-7.0866,0ZM26.5669,44.2834V38.614a.7088.7088,0,0,1,.7087-.7084h9.4488a.7088.7088,0,0,1,.7087.7084v5.6694a.7091.7091,0,0,1-.7087.7089H27.2756A.709.709,0,0,1,26.5669,44.2834Zm0-11.3385V27.2757a.7089.7089,0,0,1,.7087-.7088h9.4488a.7089.7089,0,0,1,.7087.7088v5.6692a.7089.7089,0,0,1-.7087.7087H27.2756A.7088.7088,0,0,1,26.5669,32.9449Zm0,22.6772V49.9528a.7088.7088,0,0,1,.7087-.7087h9.4488a.7089.7089,0,0,1,.7087.7087v5.6693a.7089.7089,0,0,1-.7087.7085H27.2756A.7088.7088,0,0,1,26.5669,55.6221ZM43.5748,44.2834V38.614a.7088.7088,0,0,1,.7087-.7084h9.4488a.7088.7088,0,0,1,.7087.7084v5.6694a.7091.7091,0,0,1-.7087.7089H44.2835A.709.709,0,0,1,43.5748,44.2834Zm0-11.3385V27.2757a.7089.7089,0,0,1,.7087-.7088h9.4488a.7089.7089,0,0,1,.7087.7088v5.6692a.7089.7089,0,0,1-.7087.7087H44.2835A.7088.7088,0,0,1,43.5748,32.9449Zm0,22.6772V49.9528a.7088.7088,0,0,1,.7087-.7087h9.4488a.7089.7089,0,0,1,.7087.7087v5.6693a.7089.7089,0,0,1-.7087.7085H44.2835A.7088.7088,0,0,1,43.5748,55.6221ZM9.559,44.2834V38.614a.7088.7088,0,0,1,.7087-.7084h9.4488a.7088.7088,0,0,1,.7087.7084v5.6694a.709.709,0,0,1-.7087.7089H10.2677A.7091.7091,0,0,1,9.559,44.2834Zm0-11.3385V27.2757a.7089.7089,0,0,1,.7087-.7088h9.4488a.7089.7089,0,0,1,.7087.7088v5.6692a.7088.7088,0,0,1-.7087.7087H10.2677A.7089.7089,0,0,1,9.559,32.9449Zm0,22.6772V49.9528a.7089.7089,0,0,1,.7087-.7087h9.4488a.7088.7088,0,0,1,.7087.7087v5.6693a.7088.7088,0,0,1-.7087.7085H10.2677A.7089.7089,0,0,1,9.559,55.6221Z"></path></svg><div class="date-information">${timeDiff(Date.now(),guildRun.Timestamp)}<span class="timestamp">${new Date(guildRun.Timestamp).toLocaleString("en-US", {timeZone: "America/Los_Angeles", year: 'numeric', month: 'long', day: 'numeric', timeZoneName: 'short', hour: 'numeric', minute: 'numeric', second: 'numeric'})}</span></div></div>
        </div>
      </div>
      <ul class="dungeon-group">
        <li class="dungeon-group-icon dungeon-group-icon--tank member--${group.tank.member}">${group.tank.name}</li>
        <li class="dungeon-group-icon dungeon-group-icon--healer member--${group.healer.member}">${group.healer.name}</li>
        <li class="dungeon-group-icon dungeon-group-icon--dps member--${group.dps[0].member}">${group.dps[0].name}</li>
        <li class="dungeon-group-icon dungeon-group-icon--dps member--${group.dps[1].member}">${group.dps[1].name}</li>
        <li class="dungeon-group-icon dungeon-group-icon--dps member--${group.dps[2].member}">${group.dps[2].name}</li>   
      </ul></div>`;

      run.innerHTML = runHTML;
      document.querySelector('#gmih-leaderboard').appendChild(run);


    })
  }

  document.addEventListener('DOMContentLoaded', function(event) {
    fetch('/data/leaderboard.json')
      .then(response => response.json())
      .then(data => createLeaderboard(data));
  })
</script>