<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HKDSE Economics Minimum Wage Simulator</title>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: #f5f7fa; color: #333; padding: 20px; display: flex; justify-content: center; }
        .container { max-width: 900px; width: 100%; background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.05); }
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
        .flex-container { display: grid; grid-template-columns: 1fr 280px; gap: 20px; }
        canvas { background: #ffffff; border: 1px solid #e2e8f0; width: 100%; height: auto; display: block; border-radius: 8px; }
        .analysis-box { background: #f8fafc; border: 1px solid #e2e8f0; padding: 15px; border-radius: 8px; font-size: 13px; line-height: 1.5; }
        .analysis-box h3 { margin-top: 0; color: #1e293b; font-size: 14px; border-bottom: 1px solid #e2e8f0; padding-bottom: 5px; }
        .text-gain { color: #059669; font-weight: bold; }
        .text-loss { color: #dc2626; font-weight: bold; }
    </style>
</head>
<body>

<div class="container">
    <h1>HKDSE Economics Minimum Wage Simulator</h1>
    <div class="subtitle">An interactive look at Market Intervention (Price Floors) aligning with HKDSE Paper 1 & Paper 2 structures.</div>
    
    <div class="control-panel">
        <div>
            <label for="wageSlider">Minimum Wage Rate ($/Hr): $<span id="wageVal">46</span></label>
            <input type="range" id="wageSlider" min="25" max="60" value="46" step="1">
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
            <div class="stat-num" id="statEmp">88.0</div>
        </div>
        <div class="stat-card">
            <div class="stat-title">Involuntary Unemp.</div>
            <div class="stat-num" id="statUnemp">24.0</div>
        </div>
        <div class="stat-card" id="earningsCard">
            <div class="stat-title">Total Wage Earnings</div>
            <div class="stat-num" id="statEarnings">$4,048</div>
        </div>
        <div class="stat-card" id="dwlCard">
            <div class="stat-title">Deadweight Loss</div>
            <div class="stat-num" id="statDwl">$36</div>
        </div>
    </div>

    <div class="flex-container">
        <canvas id="marketGraph" width="550" height="400"></canvas>
        <div class="analysis-box">
            <h3>HKDSE Exam Insights</h3>
            <div id="analysisText">Loading economic breakdown...</div>
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

    // Constant Equilibrium Conditions fixed for HKDSE baseline
    const We = 40;
    const Qe = 100;
    const originalRevenue = We * Qe; // $4000

    function drawMarket() {
        const W_floor = parseFloat(wageSlider.value);
        const elasticity = elasticitySelect.value;
        wageVal.innerText = W_floor;

        const isEffective = W_floor > We;
        
        // Exact mathematical configurations to safely represent DSE Elastic vs Inelastic scenarios
        let Qd = Qe;
        let Qs = Qe;
        let slopeD = elasticity === 'inelastic' ? 2.0 : 4.5;
        let slopeS = 2.0; // Standard upward sloping supply

        if (isEffective) {
            Qd = Qe - slopeD * (W_floor - We);
            Qs = Qe + slopeS * (W_floor - We);
        }

        const actualEmp = isEffective ? Qd : Qe;
        const surplus = isEffective ? (Qs - Qd) : 0;
        const totalEarnings = (isEffective ? W_floor : We) * actualEmp;
        
        // Calculate Deadweight Loss (Triangle area between S & D from transacted quantity to Qe)
        // At Qd, the difference in reservation values along the demand and supply line height:
        const dwl = isEffective ? 0.5 * (W_floor - (We - (Qe - Qd) / slopeS)) * (Qe - Qd) : 0;

        // Data adjustments
        statEmp.innerText = actualEmp.toFixed(1);
        statUnemp.innerText = surplus.toFixed(1);
        statEarnings.innerText = '$' + Math.round(totalEarnings);
        statDwl.innerText = isEffective ? '$' + Math.round(dwl) : '$0';
        
        // UI Condition Changes
        if (isEffective) {
            statusBadge.innerText = "Effective Minimum Wage (Price Floor)";
            statusBadge.className = "badge effective";
            dwlCard.className = "stat-card loss";
            
            if (totalEarnings > originalRevenue) {
                earningsCard.className = "stat-card gain";
                analysisText.innerHTML = `
                    <b>Market State:</b> Effective Price Floor (${W_floor} &gt; ${We})<br><br>
                    <b>Total Revenue Effect:</b><br>
                    Because labor demand is <span class="text-gain">Inelastic</span>, the percentage increase in wage rate outweighs the percentage decrease in employment.<br>
                    <br>• Gain Area (Green Area) &gt; Loss Area (Red Area).
                    <br>• Total wage income <span class="text-gain">increased</span> from $4,000 to $${Math.round(totalEarnings)}.<br><br>
                    <b>Efficiency Impact:</b><br>
                    A labor surplus (involuntary unemployment) of <b>${surplus.toFixed(1)} units</b> exists. A <b>Deadweight Loss</b> of $${Math.round(dwl)} is generated due to underproduction of labor.
                `;
            } else {
                earningsCard.className = "stat-card loss";
                analysisText.innerHTML = `
                    <b>Market State:</b> Effective Price Floor (${W_floor} &gt; ${We})<br><br>
                    <b>Total Revenue Effect:</b><br>
                    Because labor demand is <span class="text-loss">Elastic</span>, the percentage decrease in employment is larger than the percentage increase in wage rate.<br>
                    <br>• Loss Area (Red Area) &gt; Gain Area (Green Area).
                    <br>• Total wage income <span class="text-loss">decreased</span> from $4,000 to $${Math.round(totalEarnings)}.<br><br>
                    <b>Efficiency Impact:</b><br>
                    Firms cut back hours aggressively. Labor surplus reaches <b>${surplus.toFixed(1)} units</b>. Deadweight loss expands significantly.
                `;
            }
        } else {
            statusBadge.innerText = "Ineffective Minimum Wage (No Market Impact)";
            statusBadge.className = "badge ineffective";
            earningsCard.className = "stat-card";
            dwlCard.className = "stat-card";
            analysisText.innerHTML = `
                <b>Market State:</b> Ineffective Price Floor (${W_floor} &le; ${We})<br><br>
                <b>Total Revenue Effect:</b><br>
                The minimum wage is set below or at the equilibrium market price. Market forces safely guide the wage rate back to the stable equilibrium point <b>$${We}</b>.<br><br>
                • Employment stays at <b>${Qe}</b>.<br>
                • Total Wage revenue stays safely locked at baseline equilibrium <b>$4,000</b> (Shaded gray square).<br>
                • No surplus or Deadweight Loss is induced.
            `;
        }

        // --- GRAPH CANVAS RENDERING ---
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        
        const padding = 60;
        const graphWidth = canvas.width - padding * 2;
        const graphHeight = canvas.height - padding * 2;

        function getX(q) { return padding + (q / 160) * graphWidth; }
        function getY(w) { return canvas.height - padding - (w / 70) * graphHeight; }

        // Draw Shaded Areas
        if (isEffective) {
            // Gain Area (Green Box): Above We, up to W_floor, bounded by Qd
            ctx.fillStyle = 'rgba(16, 185, 129, 0.2)';
            ctx.fillRect(getX(0), getY(W_floor), getX(Qd) - getX(0), getY(We) - getY(W_floor));
            
            // Loss Area (Red Box): Below We down to 0, from Qd out to Qe
            ctx.fillStyle = 'rgba(239, 68, 68, 0.2)';
            ctx.fillRect(getX(Qd), getY(We), getX(Qe) - getX(Qd), getY(0) - getY(We));

            // Deadweight Loss Area (Orange Triangle): Between Demand and Supply from Qd to Qe
            ctx.fillStyle = 'rgba(249, 115, 22, 0.25)';
            ctx.beginPath();
            ctx.moveTo(getX(Qd), getY(W_floor));
            ctx.lineTo(getX(Qe), getY(We));
            ctx.lineTo(getX(Qd), getY(We - (Qe - Qd) / slopeS));
            ctx.closePath();
            ctx.fill();
        } else {
            // Ineffective state - Show constant original baseline equilibrium Total Revenue (Gray area)
            ctx.fillStyle = 'rgba(148, 163, 184, 0.15)';
            ctx.fillRect(getX(0), getY(We), getX(Qe) - getX(0), getY(0) - getY(We));
        }

        // Draw Axes
        ctx.beginPath();
        ctx.strokeStyle = '#334155';
        ctx.lineWidth = 2;
        ctx.moveTo(padding, padding);
        ctx.lineTo(padding, canvas.height - padding);
        ctx.lineTo(canvas.width - padding, canvas.height - padding);
        ctx.stroke();

        // Axis Titles
        ctx.fillStyle = '#334155';
        ctx.font = 'bold 12px sans-serif';
        ctx.fillText('Wage Rate ($)', padding - 50, padding - 15);
        ctx.fillText('Quantity of Labor (Q)', canvas.width - padding - 60, canvas.height - padding + 45);
        ctx.fillText('0', padding - 15, canvas.height - padding + 15);

        // Draw Demand Curve (D)
        ctx.beginPath();
        ctx.strokeStyle = '#2563eb';
        ctx.lineWidth = 3;
        ctx.moveTo(getX(Qe - slopeD * (60 - We)), getY(60));
        ctx.lineTo(getX(Qe - slopeD * (15 - We)), getY(15));
        ctx.stroke();
        ctx.fillText('D', getX(Qe - slopeD * (15 - We)) + 5, getY(15));

        // Draw Supply Curve (S)
        ctx.beginPath();
        ctx.strokeStyle = '#ea580c';
        ctx.lineWidth = 3;
        ctx.moveTo(getX(Qe + slopeS * (15 - We)), getY(15));
        ctx.lineTo(getX(Qe + slopeS * (60 - We)), getY(60));
        ctx.stroke();
        ctx.fillText('S', getX(Qe + slopeS * (60 - We)) + 5, getY(60));

        // Baseline Stable Equilibrium Guidance Lines
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

        // Floor Pricing Policy Visualization
        ctx.beginPath();
        ctx.strokeStyle = isEffective ? '#dc2626' : '#64748b';
        ctx.lineWidth = isEffective ? 2.5 : 1.5;
        ctx.moveTo(getX(0), getY(W_floor));
        ctx.lineTo(getX(150), getY(W_floor));
        ctx.stroke();
        
        ctx.fillStyle = isEffective ? '#dc2626' : '#64748b';
        ctx.font = 'bold 12px sans-serif';
        ctx.fillText('W_min ($' + W_floor + ')', getX(150) - 85, getY(W_floor) - 8);

        if (isEffective) {
            // Draw projection dotted lines to quantitative points Qd and Qs
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

            // Bounded intercepts highlighting surplus
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
