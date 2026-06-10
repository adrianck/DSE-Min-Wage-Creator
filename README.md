<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HKDSE Economics Minimum Wage Simulator</title>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: #f5f7fa; color: #333; padding: 20px; display: flex; justify-content: center; }
        .container { max-width: 1100px; width: 100%; background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.05); }
        h1 { color: #1e293b; margin-top: 0; margin-bottom: 5px; font-size: 24px; }
        .subtitle { color: #64748b; margin-bottom: 20px; font-size: 14px; }
        .control-panel { background: #f8fafc; border: 1px solid #e2e8f0; padding: 20px; border-radius: 8px; margin-bottom: 20px; display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        label { font-weight: bold; display: block; margin-bottom: 8px; color: #475569; }
        input[type="range"] { width: 100%; cursor: pointer; }
        select { width: 100%; padding: 8px; border-radius: 4px; border: 1px solid #cbd5e1; font-size: 15px; background: white; }
        .badge { display: inline-block; padding: 6px 12px; border-radius: 20px; font-weight: bold; margin-bottom: 15px; font-size: 14px; }
        .effective { background-color: #fee2e2; color: #991b1b; }
        .ineffective { background-color: #f1f5f9; color: #475569; }
        .stats-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; margin-bottom: 20px; }
        .stat-card { background: #f1f5f9; padding: 12px; border-radius: 8px; text-align: center; border-left: 4px solid #cbd5e1; }
        .stat-card.gain { border-left-color: #10b981; }
        .stat-card.loss { border-left-color: #ef4444; }
        .stat-title { font-size: 13px; color: #64748b; font-weight: 500; }
        .stat-num { font-size: 20px; font-weight: bold; margin-top: 5px; color: #0f172a; }
        
        /* Layout Grid for Graph, Text, and Timeline */
        .main-layout { display: grid; grid-template-columns: 1.2fr 1fr; gap: 20px; margin-bottom: 20px; }
        .left-column { display: flex; flex-direction: column; gap: 15px; }
        canvas { background: #ffffff; border: 1px solid #e2e8f0; width: 100%; height: auto; display: block; border-radius: 8px; }
        
        .right-column { display: grid; grid-template-rows: auto 1fr; gap: 15px; }
        .analysis-box { background: #f8fafc; border: 1px solid #e2e8f0; padding: 15px; border-radius: 8px; font-size: 13px; line-height: 1.5; }
        .analysis-box h3 { margin-top: 0; color: #1e293b; font-size: 14px; border-bottom: 1px solid #e2e8f0; padding-bottom: 5px; margin-bottom: 10px; }
        
        /* Timeline Styles */
        .timeline-box { background: #f8fafc; border: 1px solid #e2e8f0; padding: 15px; border-radius: 8px; font-size: 12px; overflow-y: auto; max-height: 280px; }
        .timeline-box h3 { margin-top: 0; color: #1e293b; font-size: 14px; border-bottom: 1px solid #e2e8f0; padding-bottom: 5px; margin-bottom: 10px; }
        .timeline { position: relative; padding-left: 20px; border-left: 2px solid #cbd5e1; list-style: none; margin: 0; }
        .timeline-item { margin-bottom: 12px; position: relative; }
        .timeline-item::before { content: ''; position: absolute; left: -26px; top: 3px; width: 10px; height: 10px; border-radius: 50%; background: #94a3b8; border: 2px solid white; }
        .timeline-item.active-wage::before { background: #dc2626; scale: 1.2; }
        .time-date { font-weight: bold; color: #1e293b; }
        .time-rate { color: #2563eb; font-weight: bold; }
        
        .text-gain { color: #059669; font-weight: bold; }
        .text-loss { color: #dc2626; font-weight: bold; }
    </style>
</head>
<body>

<div class="container">
    <h1>HKDSE Economics Minimum Wage Simulator</h1>
    <div class="subtitle">An interactive microeconomic laboratory tailored to Hong Kong Statutory Minimum Wage (Cap. 608) assessment criteria.</div>
    
    <div class="control-panel">
        <div>
            <label for="wageSlider">Minimum Wage Rate ($/Hr): $<span id="wageVal">43.1</span></label>
            <input type="range" id="wageSlider" min="25" max="55" value="43.1" step="0.1">
        </div>
        <div>
            <label for="elasticitySelect">Labor Demand Price Elasticity (E<sub>d</sub>)</label>
            <select id="elasticitySelect">
                <option value="inelastic" selected>Inelastic (Ed &lt; 1) | Total Earnings Rise</option>
                <option value="elastic">Elastic (Ed &gt; 1) | Total Earnings Drop</option>
            </select>
        </div>
    </div>

    <div id="statusBadge" class="badge effective">Effective Minimum Wage</div>

    <div class="stats-grid">
        <div class="stat-card">
            <div class="stat-title">Actual Employment</div>
            <div class="stat-num" id="statEmp">93.8</div>
        </div>
        <div class="stat-card">
            <div class="stat-title">Involuntary Unemp. (Surplus)</div>
            <div class="stat-num" id="statUnemp">12.4</div>
        </div>
        <div class="stat-card" id="earningsCard">
            <div class="stat-title">Total Wage Earnings (TR)</div>
            <div class="stat-num" id="statEarnings">$4,043</div>
        </div>
        <div class="stat-card" id="dwlCard">
            <div class="stat-title">Deadweight Loss (Efficiency)</div>
            <div class="stat-num" id="statDwl">$10</div>
        </div>
    </div>

    <div class="main-layout">
        <div class="left-column">
            <canvas id="marketGraph" width="550" height="400"></canvas>
        </div>
        <div class="right-column">
            <div class="analysis-box">
                <h3>HKDSE Quantitative Analysis</h3>
                <div id="analysisText">Calculating economic changes...</div>
            </div>
            <div class="timeline-box">
                <h3>Hong Kong Minimum Wage Timeline</h3>
                <ul class="timeline" id="timelineList">
                    <li class="timeline-item" data-wage="43.1"><span class="time-date">May 2026:</span> <span class="time-rate">$43.1 / hr</span> (Annual Review Formula)</li>
                    <li class="timeline-item" data-wage="42.1"><span class="time-date">May 2025:</span> <span class="time-rate">$42.1 / hr</span> (Transition Level)</li>
                    <li class="timeline-item" data-wage="40.0"><span class="time-date">May 2023:</span> <span class="time-rate">$40.0 / hr</span> (Our Base Market Equilibrium)</li>
                    <li class="timeline-item" data-wage="37.5"><span class="time-date">May 2019:</span> <span class="time-rate">$37.5 / hr</span></li>
                    <li class="timeline-item" data-wage="34.5"><span class="time-date">May 2017:</span> <span class="time-rate">$34.5 / hr</span></li>
                    <li class="timeline-item" data-wage="32.5"><span class="time-date">May 2015:</span> <span class="time-rate">$32.5 / hr</span></li>
                    <li class="timeline-item" data-wage="30.0"><span class="time-date">May 2013:</span> <span class="time-rate">$30.0 / hr</span></li>
                    <li class="timeline-item" data-wage="28.0"><span class="time-date">May 2011:</span> <span class="time-rate">$28.0 / hr</span> (Ordinance First Enacted)</li>
                </ul>
            </div>
        </div>
    </div>
</div>

<script>
    const canvas = document.getElementById('marketGraph');
    const ctx = canvas.getContext('2d');
    const wageSlider = document.getElementById('wageSlider');
    const wageVal = document.getElementById('wageVal');
    const elasticitySelect = document.getElementById('elasticitySelect');
    const statusBadge = document.getElementById('statusBadge');
    const statEmp = document.getElementById('statEmp');
    const statUnemp = document.getElementById('statUnemp');
    const statEarnings = document.getElementById('statEarnings');
    const statDwl = document.getElementById('statDwl');
    const earningsCard = document.getElementById('earningsCard');
    const dwlCard = document.getElementById('dwlCard');
    const analysisText = document.getElementById('analysisText');

    // Setting baseline equilibrium matching recent history context
    const We = 40.0;
    const Qe = 100;
    const equilibriumRevenue = We * Qe; // Fixed baseline $4000

    function drawMarket() {
        const W_floor = parseFloat(wageSlider.value);
        const elasticity = elasticitySelect.value;
        wageVal.innerText = W_floor.toFixed(1);

        const isEffective = W_floor > We;
        
        // Setup demand/supply linear curves mathematically matching DSE structures
        let Qd = Qe;
        let Qs = Qe;
        let slopeD = elasticity === 'inelastic' ? 2.0 : 4.5;
        let slopeS = 2.0;

        if (isEffective) {
            Qd = Qe - slopeD * (W_floor - We);
            Qs = Qe + slopeS * (W_floor - We);
        }

        const actualEmp = isEffective ? Qd : Qe;
        const surplus = isEffective ? (Qs - Qd) : 0;
        const totalEarnings = (isEffective ? W_floor : We) * actualEmp;
        
        // Deadweight Loss calculated from the missing transactional triangle
        const dwl = isEffective ? 0.5 * (W_floor - (We - (Qe - Qd) / slopeS)) * (Qe - Qd) : 0;

        // Visual text card updates
        statEmp.innerText = actualEmp.toFixed(1);
        statUnemp.innerText = surplus.toFixed(1);
        statEarnings.innerText = '$' + Math.round(totalEarnings);
        statDwl.innerText = isEffective ? '$' + Math.round(dwl) : '$0';
        
        // Conditional text and layout styling configurations
        if (isEffective) {
            statusBadge.innerText = "Effective Minimum Wage (Price Floor)";
            statusBadge.className = "badge effective";
            dwlCard.className = "stat-card loss";
            
            if (totalEarnings > equilibriumRevenue) {
                earningsCard.className = "stat-card gain";
                analysisText.innerHTML = `
                    <b>Market State:</b> Effective Price Floor ($${W_floor.toFixed(1)} &gt; $${We.toFixed(1)})<br><br>
                    <b>Total Expenditure / Revenue Change:</b><br>
                    Because labor demand is <span class="text-gain">Inelastic</span>, the percentage increase in the wage rate outweighs the percentage decrease in employment numbers.<br>
                    <br>• Gain Box Area &gt; Loss Box Area.
                    <br>• Total worker income <span class="text-gain">increased</span> from baseline $4,000 to $${Math.round(totalEarnings)}.<br><br>
                    <b>Efficiency Result:</b><br>
                    Creates an involuntary unemployment surplus of <b>${surplus.toFixed(1)} units</b>. Overproduction allocation leads to a <b>Deadweight Loss</b> of $${Math.round(dwl)}.
                `;
            } else {
                earningsCard.className = "stat-card loss";
                analysisText.innerHTML = `
                    <b>Market State:</b> Effective Price Floor ($${W_floor.toFixed(1)} &gt; $${We.toFixed(1)})<br><br>
                    <b>Total Expenditure / Revenue Change:</b><br>
                    Because labor demand is <span class="text-loss">Elastic</span>, the percentage drop in employment cuts deeper than the benefit of higher wages.<br>
                    <br>• Loss Box Area &gt; Gain Box Area.
                    <br>• Total worker income <span class="text-loss">decreased</span> from baseline $4,000 to $${Math.round(totalEarnings)}.<br><br>
                    <b>Efficiency Result:</b><br>
                    Firms cut hours and cut headcount aggressively. Unemployment hits <b>${surplus.toFixed(1)} units</b>. Deadweight Loss grows to $${Math.round(dwl)}.
                `;
            }
        } else {
            statusBadge.innerText = "Ineffective Minimum Wage (No Market Impact)";
            statusBadge.className = "badge ineffective";
            earningsCard.className = "stat-card";
            dwlCard.className = "stat-card";
            analysisText.innerHTML = `
                <b>Market State:</b> Ineffective Price Floor ($${W_floor.toFixed(1)} &le; $${We.toFixed(1)})<br><br>
                <b>Total Expenditure / Revenue Change:</b><br>
                Setting a minimum wage at or below equilibrium has no binding power. Market transactions naturally clear right back at the equilibrium wage point <b>$${We.toFixed(1)}</b>.<br><br>
                • Employment tracks smoothly at equilibrium <b>${Qe}</b>.<br>
                • Total Wage Revenue holds completely constant at original baseline <b>$4,000</b> (Shaded neutral box).<br>
                • No job loss or structural Deadweight Loss occurs.
            `;
        }

        // Highlights corresponding real timeline records if matching perfectly
        document.querySelectorAll('.timeline-item').forEach(item => {
            const itemWage = parseFloat(item.getAttribute('data-wage'));
            if (Math.abs(W_floor - itemWage) < 0.1) {
                item.classList.add('active-wage');
            } else {
                item.classList.remove('active-wage');
            }
        });

        // --- CANVAS GRAPH CALCULATION AND GRAPHICS ---
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        
        const padding = 60;
        const graphWidth = canvas.width - padding * 2;
        const graphHeight = canvas.height - padding * 2;

        function getX(q) { return padding + (q / 160) * graphWidth; }
        function getY(w) { return canvas.height - padding - (w / 70) * graphHeight; }

        // --- DRAW REVENUE/LOSS BOX SHADING RULES ---
        if (isEffective) {
            // Gain Box: Area between We and W_floor, up to Qd length
            ctx.fillStyle = 'rgba(16, 185, 129, 0.2)';
            ctx.fillRect(getX(0), getY(W_floor), getX(Qd) - getX(0), getY(We) - getY(W_floor));
            
            // Loss Box: Bounded area between 0 and We, from Qd out to Qe length
            ctx.fillStyle = 'rgba(239, 68, 68, 0.2)';
            ctx.fillRect(getX(Qd), getY(We), getX(Qe) - getX(Qd), getY(0) - getY(We));

            // Deadweight Loss Triangle: Area between supply and demand from Qd to Qe
            ctx.fillStyle = 'rgba(249, 115, 22, 0.25)';
            ctx.beginPath();
            ctx.moveTo(getX(Qd), getY(W_floor));
            ctx.lineTo(getX(Qe), getY(We));
            ctx.lineTo(getX(Qd), getY(We - (Qe - Qd) / slopeS));
            ctx.closePath();
            ctx.fill();
        } else {
            // INEFFECTIVE STATE REQUIREMENT: Highlight the baseline Total Revenue box, staying completely constant
            ctx.fillStyle = 'rgba(148, 163, 184, 0.15)';
            ctx.fillRect(getX(0), getY(We), getX(Qe) - getX(0), getY(0) - getY(We));
        }

        // Render Axes Lines
        ctx.beginPath();
        ctx.strokeStyle = '#334155';
        ctx.lineWidth = 2;
        ctx.moveTo(padding, padding);
        ctx.lineTo(padding, canvas.height - padding);
        ctx.lineTo(canvas.width - padding, canvas.height - padding);
        ctx.stroke();

        // Labels
        ctx.fillStyle = '#334155';
        ctx.font = 'bold 12px sans-serif';
        ctx.fillText('Wage Rate ($)', padding - 50, padding - 15);
        ctx.fillText('Quantity of Labor (Q)', canvas.width - padding - 60, canvas.height - padding + 45);
        ctx.fillText('0', padding - 15, canvas.height - padding + 15);

        // Render Demand Curve (D)
        ctx.beginPath();
        ctx.strokeStyle = '#2563eb';
        ctx.lineWidth = 3;
        ctx.moveTo(getX(Qe - slopeD * (60 - We)), getY(60));
        ctx.lineTo(getX(Qe - slopeD * (15 - We)), getY(15));
        ctx.stroke();
        ctx.fillText('D', getX(Qe - slopeD * (15 - We)) + 5, getY(15));

        // Render Supply Curve (S)
        ctx.beginPath();
        ctx.strokeStyle = '#ea580c';
        ctx.lineWidth = 3;
        ctx.moveTo(getX(Qe + slopeS * (15 - We)), getY(15));
        ctx.lineTo(getX(Qe + slopeS * (60 - We)), getY(60));
        ctx.stroke();
        ctx.fillText('S', getX(Qe + slopeS * (60 - We)) + 5, getY(60));

        // Base Market Equilibrium Lines (Always Drawn as reference)
        ctx.setLineDash([4, 4]);
        ctx.strokeStyle = '#94a3b8';
        ctx.lineWidth = 1.2;
        ctx.beginPath(); ctx.moveTo(getX(0), getY(We)); ctx.lineTo(getX(Qe), getY(We)); ctx.stroke();
        ctx.beginPath(); ctx.moveTo(getX(Qe), getY(0)); ctx.lineTo(getX(Qe), getY(We)); ctx.stroke();
        ctx.setLineDash([]);
        
        ctx.fillStyle = '#475569';
        ctx.font = '11px sans-serif';
        ctx.fillText('W_e ($40)', padding - 55, getY(We) + 4);
        ctx.fillText('Q_e (100)', getX(Qe) - 22, canvas.height - padding + 20);

        // Render Minimum Wage Policy Target Line
        ctx.beginPath();
        ctx.strokeStyle = isEffective ? '#dc2626' : '#64748b';
        ctx.lineWidth = isEffective ? 2.5 : 1.5;
        ctx.moveTo(getX(0), getY(W_floor));
        ctx.lineTo(getX(150), getY(W_floor));
        ctx.stroke();
        
        ctx.fillStyle = isEffective ? '#dc2626' : '#64748b';
        ctx.font = 'bold 12px sans-serif';
        ctx.fillText('W_min ($' + W_floor.toFixed(1) + ')', getX(150) - 95, getY(W_floor) - 8);

        if (isEffective) {
            // Project intercepts out to quantity labels
            ctx.setLineDash([2, 2]);
            ctx.strokeStyle = '#dc2626';
            ctx.lineWidth = 1;
            ctx.beginPath(); ctx.moveTo(getX(Qd), getY(W_floor)); ctx.lineTo(getX(Qd), getY(0)); ctx.stroke();
            ctx.beginPath(); ctx.moveTo(getX(Qs), getY(W_floor)); ctx.lineTo(getX(Qs), getY(0)); ctx.stroke();
            ctx.setLineDash([]);
            
            ctx.fillStyle = '#dc2626';
            ctx.font = '11px sans-serif';
            ctx.fillText('Q_d', getX(Qd) - 8, canvas.height - padding + 20);
            ctx.fillText('Q_s', getX(Qs) - 8, canvas.height - padding + 20);

            // Pinpoint nodes representing surplus endpoints
            ctx.beginPath();
            ctx.fillStyle = '#ef4444';
            ctx.arc(getX(Qd), getY(W_floor), 5, 0, 2 * Math.PI);
            ctx.arc(getX(Qs), getY(W_floor), 5, 0, 2 * Math.PI);
            ctx.fill();
        }
    }

    wageSlider.addEventListener('input', drawMarket);
    elasticitySelect.addEventListener('change', drawMarket);
    drawMarket();
</script>

</body>
</html>
