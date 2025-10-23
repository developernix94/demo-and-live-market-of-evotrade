<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>EvoTrade Dashboard</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
  <style>
    *{margin:0;padding:0;box-sizing:border-box;font-family:'Inter',sans-serif;}
    body{background:#0d0d0d;color:#fff;display:flex;flex-direction:column;min-height:100vh;}
    .topbar{display:flex;justify-content:space-between;align-items:center;padding:10px 20px;background:#111;}
    .topbar .logo{font-size:1.5rem;font-weight:700;color:#00ffd5;}
    .topbar .balances div{margin-right:15px;}
    .topbar .top-controls button{margin-left:10px;padding:5px 10px;border:none;border-radius:5px;cursor:pointer;}
    .main{display:flex;flex:1;}
    .sidebar-left{width:200px;background:#111;padding:15px;display:flex;flex-direction:column;gap:15px;}
    .sidebar-left h3{margin-bottom:10px;color:#00ffd5;}
    .sidebar-left button{padding:8px;background:#222;border:none;border-radius:5px;color:#fff;cursor:pointer;text-align:left;}
    .center{flex:1;padding:15px;display:flex;flex-direction:column;gap:15px;}
    .chart-container{flex:1;background:#111;border-radius:10px;padding:10px;}
    .order-panels{display:flex;gap:15px;margin-top:10px;}
    .order-panel{flex:1;background:#111;padding:15px;border-radius:10px;display:flex;flex-direction:column;gap:10px;}
    .order-panel h4{margin-bottom:5px;color:#00ffd5;}
    .order-panel select,input{padding:8px;border:none;border-radius:5px;width:100%;}
    .order-panel button{padding:10px;border:none;border-radius:5px;cursor:pointer;font-weight:600;}
    .order-panel .buy{background:#28a745;color:#fff;}
    .order-panel .sell{background:#ff4d4f;color:#fff;}
    .sidebar-right{width:250px;background:#111;padding:15px;display:flex;flex-direction:column;gap:10px;}
    .asset{display:flex;justify-content:space-between;padding:8px;background:#222;border-radius:5px;cursor:pointer;}
    .asset:hover{background:#333;}
    .bottom{background:#111;padding:10px 15px;display:flex;gap:15px;flex-wrap:wrap;}
    .bottom .history{flex:1;min-width:300px;}
    .bottom table{width:100%;border-collapse:collapse;}
    .bottom th,.bottom td{padding:8px;text-align:left;border-bottom:1px solid #222;}
    .profit{color:#28a745;}
    .loss{color:#ff4d4f;}
  </style>
</head>
<body>

  <!-- Top Bar -->
  <div class="topbar">
    <div class="logo">EvoTrade</div>
    <div class="balances">
      <div>Live Balance: $<span id="liveBalance">5000</span></div>
      <div>Demo Balance: $<span id="demoBalance">10000</span></div>
    </div>
    <div class="top-controls">
      <button>Deposit</button>
      <button>Withdraw</button>
      <button>Profile</button>
    </div>
  </div>

  <div class="main">
    <!-- Left Sidebar -->
    <div class="sidebar-left">
      <h3>Markets</h3>
      <button onclick="selectMarket('Crypto')">Crypto</button>
      <button onclick="selectMarket('Forex')">Forex</button>
      <button onclick="selectMarket('Stocks')">Stocks</button>
    </div>

    <!-- Center Panel -->
    <div class="center">
      <div class="chart-container">
        <canvas id="marketChart"></canvas>
      </div>
      <div class="order-panels">
        <!-- Live Panel -->
        <div class="order-panel live-panel">
          <h4>LIVE</h4>
          <select id="liveAssetSelect"></select>
          <input type="number" id="liveTradeAmount" placeholder="Amount ($)">
          <input type="number" id="liveStopLoss" placeholder="Stop-Loss">
          <input type="number" id="liveTakeProfit" placeholder="Take-Profit">
          <button class="buy" onclick="placeTrade('LIVE','BUY')">BUY</button>
          <button class="sell" onclick="placeTrade('LIVE','SELL')">SELL</button>
        </div>
        <!-- Demo Panel -->
        <div class="order-panel demo-panel">
          <h4>DEMO</h4>
          <select id="demoAssetSelect"></select>
          <input type="number" id="demoTradeAmount" placeholder="Amount ($)">
          <input type="number" id="demoStopLoss" placeholder="Stop-Loss">
          <input type="number" id="demoTakeProfit" placeholder="Take-Profit">
          <button class="buy" onclick="placeTrade('DEMO','BUY')">BUY</button>
          <button class="sell" onclick="placeTrade('DEMO','SELL')">SELL</button>
        </div>
      </div>
    </div>

    <!-- Right Sidebar -->
    <div class="sidebar-right" id="assetList"></div>
  </div>

  <!-- Bottom Panel -->
  <div class="bottom">
    <div class="history">
      <h4>LIVE Trade History</h4>
      <table>
        <thead>
          <tr>
            <th>Asset</th><th>Type</th><th>Amount</th><th>PnL</th>
          </tr>
        </thead>
        <tbody id="liveTradeHistory"></tbody>
      </table>
    </div>
    <div class="history">
      <h4>DEMO Trade History</h4>
      <table>
        <thead>
          <tr>
            <th>Asset</th><th>Type</th><th>Amount</th><th>PnL</th>
          </tr>
        </thead>
        <tbody id="demoTradeHistory"></tbody>
      </table>
    </div>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script>
    // Market & Assets
    let marketType='Crypto';
    let assets={
      Crypto:['BTC','ETH','BNB','USDT','XRP','ADA','DOGE','SOL','MATIC','DOT'],
      Forex:['USD/EUR','USD/JPY','USD/GBP','USD/CHF','USD/CAD','USD/AUD','USD/NZD'],
      Stocks:['AAPL','MSFT','AMZN','GOOGL','TSLA','META','NVDA','JPM','JNJ','V']
    };
    let marketPrices={};
    let liveBalance=5000;
    let demoBalance=10000;
    let liveHistory=[],demoHistory=[];
    let chart;

    // Initialize Prices
    function initPrices(){
      for(let type in assets){
        assets[type].forEach(a=>marketPrices[a]=Math.floor(Math.random()*1000)+50);
      }
    }

    // Update Asset List
    function updateAssetList(){
      const list=document.getElementById('assetList');
      list.innerHTML='';
      assets[marketType].forEach(a=>{
        const price=marketPrices[a].toFixed(2);
        const div=document.createElement('div');
        div.className='asset';
        div.innerHTML=`<span>${a}</span><span>$${price}</span>`;
        div.onclick=()=>{
          document.getElementById('liveAssetSelect').value=a;
          document.getElementById('demoAssetSelect').value=a;
        }
        list.appendChild(div);
      });
      // Update selects
      ['liveAssetSelect','demoAssetSelect'].forEach(id=>{
        const sel=document.getElementById(id);
        sel.innerHTML='';
        assets[marketType].forEach(a=>{
          const opt=document.createElement('option');
          opt.value=a; opt.innerText=a;
          sel.appendChild(opt);
        });
      });
    }

    // Market Selection
    function selectMarket(type){
      marketType=type;
      updateAssetList();
    }

    // Price Simulation
    function simulatePriceChange(){
      for(let a in marketPrices){
        let change=(Math.random()-0.5)*5;
        marketPrices[a]+=change;
      }
      updateAssetList();
      updateChart();
    }

    // Place Trade
    function placeTrade(mode,type){
      const asset=document.getElementById(mode.toLowerCase()+'AssetSelect').value;
      const amount=parseFloat(document.getElementById(mode.toLowerCase()+'TradeAmount').value);
      if(isNaN(amount)||amount<=0){ alert('Enter valid amount'); return; }
      const pnl=(Math.random()-0.5)*amount; // simulated PnL
      if(mode==='LIVE') liveBalance+=pnl;
      else demoBalance+=pnl;
      document.getElementById('liveBalance').innerText=liveBalance.toFixed(2);
      document.getElementById('demoBalance').innerText=demoBalance.toFixed(2);
      const entry={asset,type,amount,pnl};
      if(mode==='LIVE') liveHistory.push(entry);
      else demoHistory.push(entry);
      renderTradeHistory();
    }

    // Render Trade History
    function renderTradeHistory(){
      const tbodyLive=document.getElementById('liveTradeHistory');
      tbodyLive.innerHTML='';
      liveHistory.slice(-10).reverse().forEach(t=>{
        const tr=document.createElement('tr');
        tr.innerHTML=`<td>${t.asset}</td><td>${t.type}</td><td>$${t.amount}</td><td class="${t.pnl>=0?'profit':'loss'}">${t.pnl.toFixed(2)}</td>`;
        tbodyLive.appendChild(tr);
      });
      const tbodyDemo=document.getElementById('demoTradeHistory');
      tbodyDemo.innerHTML='';
      demoHistory.slice(-10).reverse().forEach(t=>{
        const tr=document.createElement('tr');
        tr.innerHTML=`<td>${t.asset}</td><td>${t.type}</td><td>$${t.amount}</td><td class="${t.pnl>=0?'profit':'loss'}">${t.pnl.toFixed(2)}</td>`;
        tbodyDemo.appendChild(tr);
      });
    }

    // Chart
    function initChart(){
      const ctx=document.getElementById('marketChart').getContext('2d');
      chart=new Chart(ctx,{
        type:'line',
        data:{labels:[],datasets:[{label:'Price',data:[],borderColor:'#00ffd5',borderWidth:2,fill:false}]},
        options:{animation:false,responsive:true,scales:{x:{display:true},y:{display:true}}}
      });
    }

    function updateChart(){
      const asset=document.getElementById('liveAssetSelect').value||assets[marketType][0];
      const dataArr=chart.data.datasets[0].data;
      const labelsArr=chart.data.labels;
      if(dataArr.length>20){ dataArr.shift(); labelsArr.shift(); }
      dataArr.push(market
