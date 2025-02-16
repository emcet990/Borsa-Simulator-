<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Borsa Simulator</title>

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
</head>
<body>

    <h1>Oyun Başlığı</h1>
    
    <!-- OYUNUN KODLARI BURADA ÇALIŞIYOR OLABİLİR -->
    <div id="oyun"></div>
    
    <!-- Reklam Kodu -->
    <ins class="adsbygoogle"
         style="display:block"
         data-ad-client="ca-pub-XXXXXXXXXXXXXX"
         data-ad-slot="XXXXXXXXXX"
         data-ad-format="auto"></ins>
    <script>
         (adsbygoogle = window.adsbygoogle || []).push({});
    </script>
  
  <style>
    /* Global Tasarım */
    @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');
    body {
      font-family: 'Roboto', sans-serif;
      background: linear-gradient(135deg, #1e3c72, #2a5298);
      color: #fff;
      margin: 0;
      padding: 20px;
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    h1, h2 {
      text-align: center;
      margin-bottom: 10px;
    }
    .container {
      background: rgba(0, 0, 0, 0.5);
      border-radius: 10px;
      padding: 20px;
      width: 90%;
      max-width: 800px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
      margin-bottom: 20px;
      text-align: center;
    }
    button {
      padding: 10px 20px;
      font-size: 16px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      background-color: #007BFF;
      color: #fff;
      transition: background-color 0.3s ease;
      margin: 5px;
    }
    button:hover {
      background-color: #0056b3;
    }
    .item-list {
      margin: 10px 0;
      max-height: 150px;
      overflow-y: auto;
      background: rgba(255, 255, 255, 0.1);
      padding: 10px;
      border-radius: 5px;
      text-align: left;
    }
    .item {
      margin-bottom: 5px;
    }
    /* Para Bölümü: Siyah blok, ortalanmış */
    #moneySection {
      background: #000;
      border-radius: 10px;
      padding: 20px;
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    #moneyButton {
      border: 2px solid #fff;
      padding: 20px 40px;
      border-radius: 5px;
      cursor: pointer;
      font-size: 32px;
      font-weight: bold;
      background: #333;
      margin-bottom: 10px;
    }
    /* Modal stil */
    .modal {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.7);
      display: none;
      justify-content: center;
      align-items: center;
      z-index: 1000;
    }
    .modal-content {
      background: rgba(0,0,0,0.8);
      padding: 20px;
      border-radius: 10px;
      width: 90%;
      max-width: 600px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.5);
      text-align: center;
    }
  </style>
</head>
<body>
  <!-- Start Ekranı -->
  <div id="menu" class="container">
    <h1>Borsa Simulator</h1>
    <button id="startBtn">Start</button>
  </div>

  <!-- Oyun Ekranı -->
  <div id="game" style="display:none;">
    <!-- Para Bölümü -->
    <div id="moneySection" class="container">
      <button id="moneyButton">$</button>
      <span id="moneyAmount">0</span>
    </div>

    <!-- İşlem Butonları -->
    <div class="container">
      <button id="showStocksModalBtn">Hisse Satın Al</button>
      <button id="showRealEstateModalBtn">Gayrimenkul Satın Al</button>
    </div>

    <!-- Hisselerim -->
    <div id="stocksSection" class="container">
      <h2>Hisselerim</h2>
      <div id="stocksDisplay" class="item-list"></div>
    </div>

    <!-- Gayrimenkullerim -->
    <div id="realEstateSection" class="container">
      <h2>Gayrimenkullerim</h2>
      <div id="realEstateDisplay" class="item-list"></div>
    </div>

    <!-- Ödüller Bölümü (Önizleme) -->
    <div id="rewardsSection" class="container">
      <h2>Gelecek Ödüller</h2>
      <div id="rewardsPreview" class="item-list"></div>
    </div>

    <div class="container">
      <button id="closeBtn">Close Game</button>
    </div>
  </div>

  <!-- Hisse Satın Al Modal -->
  <div id="stocksModal" class="modal">
    <div class="modal-content">
      <h2>Hisse Satın Al</h2>
      <div id="stocksModalList" class="item-list"></div>
      <button id="stocksModalBackBtn">Geri Dön</button>
    </div>
  </div>

  <!-- Gayrimenkul Satın Al Modal -->
  <div id="realEstateModal" class="modal">
    <div class="modal-content">
      <h2>Gayrimenkul Satın Al</h2>
      <div id="realEstateModalList" class="item-list"></div>
      <button id="realEstateModalBackBtn">Geri Dön</button>
    </div>
  </div>

  <script>
    // Durum değişkenleri
    let money = 0;
    let stocks = {};       // { stockName: quantity }
    let realEstate = {};   // { estateName: quantity }
    let claimedRewards = []; // Alınan ödül eşikleri

    // Hisse verileri: 50 adet, her biri farklı fiyat ve pasif gelir (küçükten büyüğe)
    const stockNames = [
      "Tesla", "Apple", "Microsoft", "Amazon", "Google", "Facebook", "Netflix", "IBM", "Intel", "Oracle",
      "Samsung", "Sony", "LG", "Nvidia", "AMD", "Dell", "HP", "Lenovo", "Asus", "Acer",
      "Xiaomi", "Huawei", "Panasonic", "Philips", "Bosch", "Siemens", "Boeing", "Lockheed", "Raytheon", "General Electric",
      "General Motors", "Ford", "Toyota", "Honda", "Nissan", "Volkswagen", "BMW", "Mercedes", "Audi", "Porsche",
      "Ferrari", "Lamborghini", "McLaren", "Bugatti", "Subaru", "Mazda", "Kia", "Hyundai", "Renault", "Peugeot"
    ];
    const stocksData = [];
    // Her bir hisse için: fiyat = 50 + index*5, pasif gelir = fiyat * 0.04
    stockNames.forEach((name, index) => {
      let price = 50 + index * 5;
      let passiveIncome = price * 0.04;
      stocksData.push({
        name: name,
        price: price,
        passiveIncome: passiveIncome
      });
    });
    // Küçükten büyüğe sıralama (fiyatına göre)
    stocksData.sort((a, b) => a.price - b.price);

    // Gayrimenkul verileri: 20 adet, her biri farklı isim, gerçekçi fiyatlar ve pasif gelir
    const realEstateNames = [
      "Ev", "Ofis", "Otel", "Arsa", "Market", "Villa", "Müstakil Ev", "Apartman", "Rezidans", "Bahçe",
      "Çiftlik", "Lojman", "Konut", "İşyeri", "Depo", "Atölye", "Sera", "Köşk", "Yalı", "Mahalle Evleri"
    ];
    const realEstateData = [];
    realEstateNames.forEach((name, index) => {
      realEstateData.push({
        name: name,
        price: 100000 + index * 50000,
        passiveIncome: 1000 + index * 500
      });
    });

    // Ödül sistemi: ilk eşik 1000₺, sonraki ödül eşiği 10 kat artar
    function getRewardThreshold(n) {
      return 1000 * Math.pow(10, n - 1);
    }
    function getRewardBonus(n) {
      return getRewardThreshold(n) / 10;
    }

    // Günlük bonus miktarı
    const dailyBonusAmount = 200;

    // Elementler
    const startBtn = document.getElementById('startBtn');
    const menuSection = document.getElementById('menu');
    const gameSection = document.getElementById('game');
    const moneyButton = document.getElementById('moneyButton');
    const moneyAmountSpan = document.getElementById('moneyAmount');
    const showStocksModalBtn = document.getElementById('showStocksModalBtn');
    const showRealEstateModalBtn = document.getElementById('showRealEstateModalBtn');
    const stocksDisplay = document.getElementById('stocksDisplay');
    const realEstateDisplay = document.getElementById('realEstateDisplay');
    const rewardsPreview = document.getElementById('rewardsPreview');
    const closeBtn = document.getElementById('closeBtn');

    const stocksModal = document.getElementById('stocksModal');
    const stocksModalList = document.getElementById('stocksModalList');
    const stocksModalBackBtn = document.getElementById('stocksModalBackBtn');

    const realEstateModal = document.getElementById('realEstateModal');
    const realEstateModalList = document.getElementById('realEstateModalList');
    const realEstateModalBackBtn = document.getElementById('realEstateModalBackBtn');

    // Zaman kontrolü: son oynanma, günlük bonus
    const lastLoginKey = "lastLogin";
    const lastDailyBonusKey = "lastDailyBonus";

    // Durumu yükle (varsa)
    function loadState() {
      money = parseFloat(localStorage.getItem("money")) || 0;
      stocks = JSON.parse(localStorage.getItem("stocks")) || {};
      realEstate = JSON.parse(localStorage.getItem("realEstate")) || {};
      claimedRewards = JSON.parse(localStorage.getItem("claimedRewards")) || [];
    }
    loadState();

    // Günlük bonus kontrolü
    function checkDailyBonus() {
      let today = new Date().toDateString();
      let lastBonus = localStorage.getItem(lastDailyBonusKey);
      if(lastBonus !== today) {
        money += dailyBonusAmount;
        alert("Günlük bonusun: " + dailyBonusAmount + "$ kazandın!");
        localStorage.setItem(lastDailyBonusKey, today);
        updateMoneyDisplay();
      }
    }

    // Eğer son oynanma 24 saatten uzun ise teşvik mesajı göster
    function checkInactive() {
      let lastLogin = localStorage.getItem(lastLoginKey);
      if(lastLogin) {
        let diff = new Date().getTime() - new Date(lastLogin).getTime();
        if(diff > 24 * 60 * 60 * 1000) {
          alert("Uzun zamandır oynamadın! Gel, yeni ödüller ve bonuslarla oyunu tekrar keşfet!");
        }
      }
      localStorage.setItem(lastLoginKey, new Date().toString());
    }

    // Kaydetme fonksiyonu (otomatik)
    function saveState() {
      localStorage.setItem("money", money);
      localStorage.setItem("stocks", JSON.stringify(stocks));
      localStorage.setItem("realEstate", JSON.stringify(realEstate));
      localStorage.setItem("claimedRewards", JSON.stringify(claimedRewards));
    }
    window.addEventListener('beforeunload', saveState);

    // Ekran güncellemeleri
    function updateMoneyDisplay() {
      moneyAmountSpan.textContent = money.toFixed(2);
      checkRewards();
      updateRewardsPreview();
    }

    function updateStocksDisplay() {
      stocksDisplay.innerHTML = '';
      for (let key in stocks) {
        let div = document.createElement('div');
        div.classList.add('item');
        div.textContent = key + " - " + stocks[key] + " adet";
        stocksDisplay.appendChild(div);
      }
    }

    function updateRealEstateDisplay() {
      realEstateDisplay.innerHTML = '';
      for (let key in realEstate) {
        let div = document.createElement('div');
        div.classList.add('item');
        div.textContent = key + " - " + realEstate[key] + " adet";
        realEstateDisplay.appendChild(div);
      }
    }

    // Ödül önizlemesi: Alınan ödüller sayısına göre sonraki 10 ödül eşiğini göster
    function updateRewardsPreview() {
      rewardsPreview.innerHTML = '';
      let nextRewardLevel = claimedRewards.length + 1;
      for (let i = 0; i < 10; i++) {
        let n = nextRewardLevel + i;
        let threshold = getRewardThreshold(n);
        let bonus = getRewardBonus(n);
        let div = document.createElement('div');
        div.classList.add('item');
        div.textContent = "Eşik: $" + threshold + " - Bonus: $" + bonus;
        rewardsPreview.appendChild(div);
      }
    }

    // Ödül kontrolü: para, ödül eşiğini aşarsa bonus ver
    function checkRewards() {
      let n = 1;
      while(true) {
        let threshold = getRewardThreshold(n);
        if(money >= threshold && !claimedRewards.includes(threshold)) {
          let bonus = getRewardBonus(n);
          money += bonus;
          claimedRewards.push(threshold);
          alert("Tebrikler! " + threshold + "$'ı aştın. " + bonus + "$ bonus kazandın!");
          updateMoneyDisplay();
        } else if(money < threshold) {
          break;
        }
        n++;
      }
    }

    // Modal Gösterimleri
    showStocksModalBtn.addEventListener('click', function() {
      populateStocksModal();
      stocksModal.style.display = 'flex';
    });
    showRealEstateModalBtn.addEventListener('click', function() {
      populateRealEstateModal();
      realEstateModal.style.display = 'flex';
    });
    stocksModalBackBtn.addEventListener('click', function() {
      stocksModal.style.display = 'none';
    });
    realEstateModalBackBtn.addEventListener('click', function() {
      realEstateModal.style.display = 'none';
    });

    // Stocks Modal Listesi (50 hisse)
    function populateStocksModal() {
      stocksModalList.innerHTML = '';
      stocksData.forEach(stock => {
        let itemDiv = document.createElement('div');
        itemDiv.classList.add('item');
        itemDiv.textContent = stock.name + " - $" + stock.price.toFixed(2) + " (Pasif: $" + stock.passiveIncome.toFixed(2) + "/tick)";
        let buyBtn = document.createElement('button');
        buyBtn.textContent = "Satın Al";
        buyBtn.addEventListener('click', function() {
          if(money >= stock.price) {
            money -= stock.price;
            stocks[stock.name] = (stocks[stock.name] || 0) + 1;
            updateMoneyDisplay();
            updateStocksDisplay();
            alert(stock.name + " satın alındı!");
          } else {
            alert("Yeterli paranız yok!");
          }
        });
        itemDiv.appendChild(buyBtn);
        // Max butonu ekle
        let maxBtn = document.createElement('button');
        maxBtn.textContent = "Max";
        maxBtn.addEventListener('click', function() {
          let count = Math.floor(money / stock.price);
          if(count > 0) {
            money -= count * stock.price;
            stocks[stock.name] = (stocks[stock.name] || 0) + count;
            updateMoneyDisplay();
            updateStocksDisplay();
            alert(stock.name + " max alındı: " + count + " adet!");
          } else {
            alert("Yeterli paranız yok!");
          }
        });
        itemDiv.appendChild(maxBtn);
        stocksModalList.appendChild(itemDiv);
      });
    }

    // Real Estate Modal Listesi (20 gayrimenkul)
    function populateRealEstateModal() {
      realEstateModalList.innerHTML = '';
      realEstateData.forEach(item => {
        let itemDiv = document.createElement('div');
        itemDiv.classList.add('item');
        itemDiv.textContent = item.name + " - $" + item.price + " (Pasif: $" + item.passiveIncome + "/tick)";
        let buyBtn = document.createElement('button');
        buyBtn.textContent = "Satın Al";
        buyBtn.addEventListener('click', function() {
          if(money >= item.price) {
            money -= item.price;
            realEstate[item.name] = (realEstate[item.name] || 0) + 1;
            updateMoneyDisplay();
            updateRealEstateDisplay();
            alert(item.name + " satın alındı!");
          } else {
            alert("Yeterli paranız yok!");
          }
        });
        itemDiv.appendChild(buyBtn);
        // Max butonu ekle
        let maxBtn = document.createElement('button');
        maxBtn.textContent = "Max";
        maxBtn.addEventListener('click', function() {
          let count = Math.floor(money / item.price);
          if(count > 0) {
            money -= count * item.price;
            realEstate[item.name] = (realEstate[item.name] || 0) + count;
            updateMoneyDisplay();
            updateRealEstateDisplay();
            alert(item.name + " max alındı: " + count + " adet!");
          } else {
            alert("Yeterli paranız yok!");
          }
        });
        itemDiv.appendChild(maxBtn);
        realEstateModalList.appendChild(itemDiv);
      });
    }

    // Pasif gelir: Her saniye, sahip olunan hisse ve gayrimenkulden gelir ekle
    setInterval(function() {
      let passive = 0;
      stocksData.forEach(stock => {
        if(stocks[stock.name]) {
          passive += stocks[stock.name] * stock.passiveIncome;
        }
      });
      realEstateData.forEach(item => {
        if(realEstate[item.name]) {
          passive += realEstate[item.name] * item.passiveIncome;
        }
      });
      if(passive > 0) {
        money += passive;
        updateMoneyDisplay();
      }
    }, 1000);

    // Start butonuna tıklanınca
    startBtn.addEventListener('click', function() {
      menuSection.style.display = 'none';
      gameSection.style.display = 'block';
      updateMoneyDisplay();
      updateStocksDisplay();
      updateRealEstateDisplay();
      updateRewardsPreview();
      checkDailyBonus();
      checkInactive();
    });

    // Oyundan çıkış butonu
    closeBtn.addEventListener('click', function() {
      saveState();
      location.reload();
    });
  </script>
</body>
</html>
