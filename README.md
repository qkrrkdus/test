<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>간단한 텍스트 RPG 게임</title>
<style>
  body {
    font-family: 'Courier New', Courier, monospace;
    text-align: center;
  }
  #log {
    height: 150px;
    border: 1px solid #000;
    margin: 20px;
    padding: 10px;
    overflow-y: auto;
  }
</style>
<script>
  var player = {
    name: "용사",
    level: 1,
    exp: 0,
    hp: 100,
    attack: 15, // 기본 공격력을 높임
    gold: 50 // 초기 골드를 50으로 설정
  };

  var currentMonster = null;

  function initializeGame() {
    player = {
      name: "용사",
      level: 1,
      exp: 0,
      hp: 100,
      attack: 15, // 기본 공격력을 높임
      gold: 50 // 초기 골드를 50으로 설정
    };
    currentMonster = null;
    document.getElementById("log").innerHTML = "<p>게임이 초기화되었습니다! 모험을 시작하세요.</p>";
    updateStatus();
  }

  function createMonster() {
    var monsters = [
      { name: "슬라임", hp: 25, attack: 5, exp: 10, gold: 3 }, // 몬스터 체력 조정
      { name: "고블린", hp: 40, attack: 10, exp: 20, gold: 6 }, // 몬스터 체력 조정
      { name: "드래곤", hp: 80, attack: 25, exp: 50, gold: 15 } // 몬스터 체력 조정
    ];
    return monsters[Math.floor(Math.random() * monsters.length)];
  }

  function getRandomInt(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  function playerAttack() {
    var log = document.getElementById("log");
    if (player.hp <= 0) {
      log.innerHTML += "<p>당신은 이미 쓰러졌습니다... 다시 시작하려면 다시 하기 버튼을 누르세요.</p>";
      return;
    }

    if (!currentMonster) {
      currentMonster = createMonster();
      log.innerHTML += "<p>야생의 " + currentMonster.name + "이(가) 나타났다! 체력: " + currentMonster.hp + "</p>";
    }
    
    var playerDamage = getRandomInt(player.attack / 2, player.attack); // 더 높은 피해를 줄 수 있도록 조정
    currentMonster.hp -= playerDamage;

    log.innerHTML += "<p>당신은 " + currentMonster.name + "에게 " + playerDamage + "의 피해를 입혔습니다! 남은 체력: " + currentMonster.hp + "</p>";

    if (currentMonster.hp <= 0) {
      log.innerHTML += "<p>당신은 " + currentMonster.name + "을(를) 이겼습니다!</p>";
      gainExpAndGold(currentMonster.exp, currentMonster.gold);
      currentMonster = null; // 몬스터를 초기화
    } else {
      monsterAttack();
    }
    updateStatus();
  }

  function monsterAttack() {
    var log = document.getElementById("log");
    var monsterDamage = getRandomInt(1, currentMonster.attack);
    player.hp -= monsterDamage;
    log.innerHTML += "<p>" + currentMonster.name + "의 반격! 당신은 " + monsterDamage + "의 피해를 받았습니다!</p>";
    if (player.hp <= 0) {
      log.innerHTML += "<p>당신은 쓰러졌습니다...</p>";
      log.innerHTML += "<p>다시 시작하려면 다시 하기 버튼을 누르세요.</p>";
    }
  }

  function gainExpAndGold(exp, gold) {
    var log = document.getElementById("log");
    player.exp += exp;
    player.gold += gold;
    log.innerHTML += "<p>" + exp + "의 경험치와 " + gold + "골드를 획득했습니다!</p>";
    checkLevelUp();
  }

  function checkLevelUp() {
    var log = document.getElementById("log");
    if (player.exp >= 100) {
      player.level++;
      player.exp -= 100;
      player.hp = 100; // HP를 전체 회복
      player.attack += 5; // 기본 공격력 상승
      log.innerHTML += "<p>레벨 업! 현재 레벨: " + player.level + "</p>";
    }
  }

  function upgradeAttack() {
    var log = document.getElementById("log");
    if (player.gold >= 10) {
      player.gold -= 10;
      player.attack += 5; // 공격력을 5만큼 증가
      log.innerHTML += "<p>공격력이 업그레이드되었습니다! 현재 공격력: " + player.attack + "</p>";
    } else {
      log.innerHTML += "<p>골드가 부족합니다! 현재 골드: " + player.gold + "</p>";
    }
    updateStatus();
  }

  function healPlayer() {
    var log = document.getElementById("log");
    if (player.gold >= 5 && player.hp < 100) {
      player.gold -= 5;
      player.hp += 5; // 체력을 5만큼 회복
      player.hp = player.hp > 100 ? 100 : player.hp; // 체력이 100을 초과하지 않도록 조정
      log.innerHTML += "<p>체력을 회복했습니다! 현재 체력: " + player.hp + "</p>";
    } else if (player.hp >= 100) {
      log.innerHTML += "<p>체력이 이미 가득 찼습니다!</p>";
    } else {
      log.innerHTML += "<p>골드가 부족합니다! 현재 골드: " + player.gold + "</p>";
    }
    updateStatus();
  }

  function updateStatus() {
    document.getElementById("status").innerHTML = "이름: " + player.name + "<br>레벨: " + player.level + "<br>경험치: " + player.exp + "<br>체력: " + player.hp + "<br>공격력: " + player.attack + "<br>골드: " + player.gold;
  }

</script>
</head>
<body onload="initializeGame()">

<h1>간단한 텍스트 RPG 게임</h1>
<p>
  <button onclick="playerAttack()">공격하기</button>
  <button onclick="upgradeAttack()">파워 업그레이드 (10골드)</button>
  <button onclick="healPlayer()">체력 회복 (5골드)</button>
  <button onclick="initializeGame()">다시 하기</button>
</p>
<div id="status"></div>
<div id="log"></div>

</body>
</html>
