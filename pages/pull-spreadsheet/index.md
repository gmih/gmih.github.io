---
layout: default
title: Update Spreadsheet
permalink: pull-spreadsheet/
---

<!--These are JavaScript Libraries-->
<script type="text/javascript" src="/js/jquery-latest.min.js"></script>
<script type="text/javascript" src="/js/utility.js"></script>
<script type="text/javascript" src="/js/cookies.js"></script>
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.1.0/papaparse.min.js"></script>
<script type="text/javascript" src="/js/js-yaml.min.js"></script>
<script type="text/javascript" src="/js/github.js"></script>

<script type="text/javascript">

    // Reset the cookie manually
    //Cookies.expire('token');

    // URL: https://[user/org].github.io/pull-spreadsheet/?key=your_key&worksheet=worksheet_name&token=your_personal_access_token&org=your_org&repo=your_repo&branch=your_branch

    // Incoming Variables append
    var $key = getUrlVar('key');
    var $resource = getUrlVar('worksheet');
    var $oAuthToken = getUrlVar('token');
    var $org = getUrlVar('org');
    var $repo = getUrlVar('repo');
    var $branch = getUrlVar('branch');

    var $yaml_store = "";

    if($oAuthToken !='')
      {
      // Setting a cookie value
      Cookies.set('token', $oAuthToken);
      // Set with expiration
      // Cookies.set('token', $oAuthToken, { expires: '01/01/2017' });
      }

    // Grab the token from cookie
    $oAuthToken = Cookies.get('token');

    
    var public_spreadsheet_url = 'https://cors-anywhere.herokuapp.com/docs.google.com/spreadsheets/d/e/2PACX-1vStHOtRnAzF2n_zQkvc2Ge1w35pXIodQwyFnrkwq8mHlopvyDPTGUnveXrYXGeuKq1BC5EVrMejeMaD/pub?gid=0&single=true&output=csv';

    function init() {
      Papa.parse(public_spreadsheet_url, {
        download: true,
        header: true,
        complete: showInfo
      })
    }

    window.addEventListener('DOMContentLoaded', init)

    function showInfo(results) {
      var data = results.data
      document.getElementById('notification').textContent = "Successfully processed " + data.length + " rows!"
      document.getElementById('source').value = JSON.stringify(data);
      saveData(JSON.stringify(data));
    }

function saveData(data) {
  //Grab the token from cookie
  $oAuthToken = Cookies.get('token');

  var github = new Github({token: $oAuthToken,auth: "oauth"});
  var repo = github.getRepo($org,$repo);

  repo.getTree($branch + '?recursive=true', function(err, tree) {

    $.each(tree, function(treeKey, treeValue) {

      $writepath = 'data/' + $resource + '.json';
      $path = treeValue['path'];
      $sha = treeValue['sha'];

      if($path==$writepath) {
        repo.writemanual('master', $writepath, encodeURIComponent(data), 'Save', $sha, function(err) { });
        console.log("writing " + $writepath);
      }
    });
  });
}

</script>
<p id="notification"></p>
<textarea cols="10" rows="5" id="source" style="border: 1px solid #000; width: 100%; height: 350px;"></textarea>