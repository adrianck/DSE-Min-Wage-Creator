<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HKDSE Economics Minimum Wage Simulator & History Archive</title>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: #f5f7fa; color: #333; padding: 20px; display: flex; justify-content: center; }
        .container { max-width: 1050px; width: 100%; background: white; padding: 30px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.05); }
        h1 { color: #1e293b; margin-top: 0; margin-bottom: 5px; font-size: 26px; }
        .subtitle { color: #64748b; margin-bottom: 25px; font-size: 14px; }
        
        .control-panel { background: #f8fafc; border: 1px solid #e2e8f0; padding: 20px; border-radius: 8px; margin-bottom: 20px; display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        label { font-weight: bold; display: block; margin-bottom: 8px; color: #475569; }
        input[type="range"] { width: 100%; cursor: pointer; }
        select { width: 100%; padding: 8px; border-radius: 4px; border: 1px solid #cbd5e1; font-size: 15px; background: white; }
        
        .badge { display: inline-block; padding: 6px 12px; border-radius: 20px; font-weight: bold; margin-bottom: 15px; font-size: 14px; }
        .effective { background-color: #fee2e2; color: #991b1b; }
        .ineffective { background-color: #f1f5f9; color: #475569; }
        
        .stats-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 15px; margin-bottom: 20px; }
        .stat-card { background: #f1f5f9; padding: 15px; border-radius: 8px; text-align: center; border-left: 4px solid #cbd5e1; }
        .stat-card.gain { border-left-color: #10b981; }
        .stat-card.loss { border-left-color: #ef4444; }
        .stat-title { font-size: 13px; color: #64748b; font-weight: 500; }
        .stat-num { font-size: 22px; font-weight: bold; margin-top: 5px; color: #0f172a; }
        
        /* Layout Structure */
        .workspace-layout { display: grid; grid-template-columns: 1.1fr 1fr; gap: 25px; margin-bottom: 30px; align-items: start; }
        canvas { background: #ffffff; border: 1px solid #e2e8f0; width: 100%; height: auto; display: block; border-radius: 8px; }
        .analysis-box { background: #f8fafc; border: 1px solid #e2e8f0; padding: 20px; border-radius: 8px; font-size: 14px; line-height: 1.6; height: 100%; box-sizing: border-box; }
        .analysis-box h3 { margin-top: 0; color: #1e293b; font-size: 16px; border-bottom: 1px solid #e2e8f0; padding-bottom: 8px; margin-bottom: 12px; }
        
        /* History & Archive Section */
        .history-section { border-top: 2px dashed #e2e8f0; padding-top: 25px; margin-top: 10px; }
        .history-section h2 { color: #0f172a; font-size: 20px; margin-top: 0; margin-bottom: 15px; }
        .pre-legislation-card { background: #f8fafc; border-left: 4px solid #3b82f6; padding: 15px; border-radius: 6px; margin-bottom: 20px; font-size: 14px; line-height: 1.5; }
        .pre-legislation-card h4 { margin: 0 0 8px 0; color: #1e293b; font-size: 15px; }
        
        /* Interactive Timeline Table */
        .table-wrapper { overflow-x: auto; }
        .timeline-table { width: 100%; border-collapse: collapse; text-align: left; font-size: 13.5px; }
        .timeline-table th { background: #f1f5f9; color: #334155; padding: 12px; font-weight: 600; border-bottom: 2px solid #cbd5e1; }
        .timeline-table td { padding: 12px; border-bottom: 1px solid #e2e8f0; }
        .timeline-table tbody tr { cursor: pointer; transition: background 0.2s; }
        .timeline-table tbody tr:hover { background: #f0fdf4; }
        .timeline-table tr.selected-row { background: #fee2e2 !important; font-weight: bold; border-left: 4px solid #dc2626; }
        
        .rate-tag { background: #e0f2fe; color: #0369a1; padding: 3px 8px; border-radius: 4px; font-weight: bold; }
        .change-tag { font-weight: 600; }
        .change-up { color: #15803d; }
        .change-flat { color: #64748b; }
        
        .text-gain { color: #059669; font-weight: bold; }
        .text-loss { color: #dc2626; font-weight: bold; }
    </style>
</head>
<body>

<div class="container">
    <h1>HKDSE Economics Minimum Wage Simulator</h1>
    <div class="subtitle">An interactive laboratory combining graphical price floor mechanics with the historical progression of Hong Kong Statutory Minimum Wage (Cap. 608).</div>
    
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
            <div class="stat-title">Involuntary Unemployment (Surplus)</div>
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

    <div class="workspace-layout">
        <canvas id="marketGraph" width="540" height="400"></canvas>
        
        <div class="analysis-box">
            <h3>HKDSE Market Evaluation</h3>
            <div id="analysisText">Simulating market metrics...</div>
        </div>
    </div>

    <div class="history-section">
        <h2>History & Archive of Minimum Wages in Hong Kong</h2>
        
        <div class="pre-legislation-card">
            <h4>The Pre-Legislation Era (Before 2011)</h4>
            <ul>
                <li><b>1932 & 1940s:</b> Early colonial regulations (like the <i>Trade Boards Ordinance</i>) allowed the Governor to set structural wage minimums for low-paid piece-rate factories, but these legal powers were never executed in practice.</li>
                <li><b>1970s:</b> A mandatory Minimum Allowable Wage (MAW) framework was deployed specifically targeting Foreign Domestic Helpers (FDHs), structured around fixed monthly contracts rather than flexible hourly grids.</li>
                <li><b>2006:</b> Facing income disparity challenges, the government launched a voluntary <i>"Wage Protection Movement"</i> for cleaning and security sectors. The clean movement yielded weak results, forcing the introduction of legally binding mandates.</li>
                <li><b>July 2010:</b> The Legislative Council formally passes the landmark <b>Minimum Wage Ordinance (Cap. 608)</b>.</li>
            </ul>
        </div>

        <h3>📈 Statutory Minimum Wage Rate Timeline (2011–2026)</h3>
        <p style="font-size: 13px; color: #64748b; margin-top: -8px; margin-bottom: 15px;">
            *Click on any historical row below to update the interactive simulation models to match that specific wage rate.
        </p>

        <div class="table-wrapper">
            <table class="timeline-table" id="historyTable">
                <thead>
                    <tr>
                        <th>Effective Date</th>
                        <th>Hourly Rate (HKD)</th>
                        <th>Change</th>
                        <th>Context / Economic Environment</th>
                    </tr>
                </thead>
                <tbody>
                    <tr data-wage="43.1">
                        <td><b>May 1, 2026</b></td>
                        <td><span class="rate-tag">$43.10</span></td>
                        <td><span class="change-tag change-up">+$1.00 (+2.4%)</span></td>
                        <td>Current approved statutory rate managed via the modern formula framework.</td>
                    </tr>
                    <tr data-wage="42.1">
                        <td>May 1, 2025</td>
                        <td><span class="rate-tag">$42.10</span></td>
                        <td><span class="change-tag change-up">+$2.10 (+5.2%)</span></td>
                        <td>First initialization layer using the shifting annual formula-based review metrics.</td>
                    </tr>
                    <tr data-wage="40.0">
                        <td>May 1, 2023</td>
                        <td><span class="rate-tag">$40.00</span></td>
                        <td><span class="change-tag change-up">+$2.50 (+6.7%)</span></td>
                        <td><b>Our Baseline Market Equilibrium ($40).</b> Increases restarted as post-pandemic retail sectors adjusted.</td>
                    </tr>
                    <tr data-wage="37.5">
                        <td>May 1, 2021</td>
                        <td><span class="rate-tag">$37.50</span></td>
                        <td><span class="change-tag change-flat">$0.00 (Frozen)</span></td>
                        <td>First historical freeze. Held stable to minimize layoffs during structural COVID downturns.</td>
                    </tr>
                    <tr data-wage="37.5">
                        <td>May 1, 2019</td>
                        <td><span class="rate-tag">$37.50</span></td>
                        <td><span class="change-tag change-up">+$3.00 (+8.7%)</span></td>
                        <td>Strong macroeconomic conditions allowed for a notable dollar adjustment.</td>
                    </tr>
                    <tr data-wage="34.5">
                        <td>May 1, 2017</td>
                        <td><span class="rate-tag">$34.50</span></td>
                        <td><span class="change-tag change-up">+$2.00 (+6.2%)</span></td>
                        <td>Steady baseline updates aiming to secure low-income frontline household purchasing power.</td>
                    </tr>
                    <tr data-wage="32.5">
                        <td>May 1, 2015</td>
                        <td><span class="rate-tag">$32.50</span></td>
                        <td><span class="change-tag change-up">+$2.50 (+8.3%)</span></td>
                        <td>Supported by continuous positive growth trends across domestic logistics and service industries.</td>
                    </tr>
                    <tr data-wage="30.0">
                        <td>May 1, 2013</td>
                        <td><span class="rate-tag">$30.00</span></td>
                        <td><span class="change-tag change-up">+$2.00 (+7.1%)</span></td>
                        <td>First cyclical updates under the original biennial assessment strategy to counteract inflation.</td>
                    </tr>
                    <tr data-wage="28.0">
                        <td>May 1, 2011</td>
                        <td><span class="rate-tag">$28.00</span></td>
                        <td><span class="change-tag change-flat">Initial Rate</span></td>
                        <td>Official operational implementation of the baseline wage floor system on Labour Day.</td>
                    </tr>
                </tbody>
            </table>
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
    const historyTableRows = document.querySelectorAll('#historyTable tbody tr');

    // Fixed Baseline Equilibrium Settings matching standard 2023 DSE timeline context
    const We = 40.0;
    const Qe = 100;
    const equilibriumRevenue = We * Qe; // $4000 baseline reference

    function drawMarket() {
        const W_floor = parseFloat(wageSlider.value);
        const elasticity = elasticitySelect.value;
        wageVal.innerText = W_floor.toFixed(1);

        const isEffective = W_floor > We;
        
        // Dynamic slopes configuration matching textbook price floor illustrations
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
        
        // Deadweight Loss calculation via transaction triangle drop height
        const dwl = isEffective ? 0.5 * (W_floor - (We - (Qe - Qd) / slopeS)) * (Qe - Qd) : 0;

        // Visual text updates
        statEmp.innerText = actualEmp.toFixed(1);
        statUnemp.innerText = surplus.toFixed(1);
        statEarnings.innerText = '$' + Math.round(totalEarnings);
        statDwl.innerText = isEffective ? '$' + Math.round(dwl) : '$0';
        
        // Comprehensive textual and analysis content updates
        if (isEffective) {
            statusBadge.innerText = "Effective Minimum Wage (Price Floor)";
            statusBadge.className = "badge effective";
            dwlCard.className = "stat-card loss";
            
            if (totalEarnings > equilibriumRevenue) {
                earningsCard.className = "stat-card gain";
                analysisText.innerHTML = `
                    <b>Policy Status:</b> Effective Price Floor ($${W_floor.toFixed(1)} &gt; $${We.toFixed(1)})<br><br>
                    <b>Total Labor Expenditure Changes:</b><br>
                    Because demand for labor is <span class="text-gain">Inelastic</span>, the percentage increase in the hourly rate is greater than the percentage drop in headcount.<br>
                    <br>• Green Rectangle Area &gt; Red Rectangle Area.
                    <br>• Total earnings <span class="text-gain">increased</span> from baseline $4,000 to $${Math.round(totalEarnings)}.<br><br>
                    <b>Efficiency Result:</b><br>
                    An involuntary unemployment surplus of <b>${surplus.toFixed(1)} workers</b> is formed. Allocative inefficiency yields a <b>Deadweight Loss</b> of $${Math.round(dwl)}.
                `;
            } else {
                earningsCard.className = "stat-card loss";
                analysisText.innerHTML = `
                    <b>Policy Status:</b> Effective Price Floor ($${W_floor.toFixed(1)} &gt; $${We.toFixed(1)})<br><br>
                    <b>Total Labor Expenditure Changes:</b><br>
                    Because demand for labor is <span class="text-loss">Elastic</span>, businesses reduce worker hours significantly to handle costs.<br>
                    <br>• Red Rectangle Area &gt; Green Rectangle Area.
                    <br>• Total earnings <span class="text-loss">decreased</span> from baseline $4,000 to $${Math.round(totalEarnings)}.<br><br>
                    <b>Efficiency Result:</b><br>
                    Employment scales back significantly, leaving an unemployment gap of <b>${surplus.toFixed(1)} units</b>. Deadweight Loss stands at $${Math.round(dwl)}.
                `;
            }
        } else {
            statusBadge.innerText = "Ineffective Minimum Wage (No Market Impact)";
            statusBadge.className = "badge ineffective";
            earningsCard.className = "stat-card";
            dwlCard.className = "stat-card";
            analysisText.innerHTML = `
                <b>Policy Status:</b> Ineffective Price Floor ($${W_floor.toFixed(1)} &le; $${We.toFixed(1)})<br><br>
                <b>Total Labor Expenditure Changes:</b><br>
                The minimum wage floor is placed below or at the natural market equilibrium. Because transactions below this line remain legal, market forces pull wages back to equilibrium <b>$${We.toFixed(1)}</b>.<br><br>
                • Total employment matches equilibrium <b>${Qe}</b>.<br>
                • Total Expenditure is constant at baseline <b>$4,000</b> (Shaded gray box).<br>
                • No job loss or structural Deadweight Loss is induced.
            `;
        }

        // Highlight matching table row based on slider inputs
        historyTableRows.forEach(row => {
            const rowWage = parseFloat(row.getAttribute('data-wage'));
            if (Math.abs(W_floor - rowWage) < 0.05) {
                row.classList.add('selected-row');
            } else {
                row.classList.remove('selected-row');
            }
        });

        // --- GRAPH CANVAS RENDERING PIPELINE ---
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        
        const padding = 60;
        const graphWidth = canvas.width - padding * 2;
        const graphHeight = canvas.height - padding * 2;

        function getX(q) { return padding + (q / 160) * graphWidth; }
        function getY(w) { return canvas.height - padding - (w / 70) * graphHeight; }

        // Render Shaded Rectangles Based on Binding Conditions
        if (isEffective) {
            // Gain Box: Area between We and W_floor up to Qd length
            ctx.fillStyle = 'rgba(16, 185, 129, 0.2)';
            ctx.fillRect(getX(0), getY(W_floor), getX(Qd) - getX(0), getY(We) - getY(W_floor));
            
            // Loss Box: Area between 0 and We from Qd out to Qe length
            ctx.fillStyle = 'rgba(239, 68, 68, 0.2)';
            ctx.fillRect(getX(Qd), getY(We), getX(Qe) - getX(Qd), getY(0) - getY(We));

            // Deadweight Loss Triangle
            ctx.fillStyle = 'rgba(249, 115, 22, 0.25)';
            ctx.beginPath();
            ctx.moveTo(getX(Qd), getY(W_floor));
            ctx.lineTo(getX(Qe), getY(We));
            ctx.lineTo(getX(Qd), getY(We - (Qe - Qd) / slopeS));
            ctx.closePath();
            ctx.fill();
        } else {
            // SHADING REQUIREMENT: Maintain constant reference equilibrium total revenue rectangle
            ctx.fillStyle = 'rgba(148, 163, 184, 0.15)';
            ctx.fillRect(getX(0), getY(We), getX(Qe) - getX(0), getY(0) - getY(We));
        }

        // Structural Axis Lines
        ctx.beginPath();
        ctx.strokeStyle = '#334155';
        ctx.lineWidth = 2;
        ctx.moveTo(padding, padding);
        ctx.lineTo(padding, canvas.height - padding);
        ctx.lineTo(canvas.width - padding, canvas.height - padding);
        ctx.stroke();

        // Titles and Text Vectors
        ctx.fillStyle = '#334155';
        ctx.font = 'bold 12px sans-serif';
        ctx.fillText('Wage Rate ($)', padding - 50, padding - 15);
        ctx.fillText('Quantity of Labor (Q)', canvas.width - padding - 60, canvas.height - padding + 45);
        ctx.fillText('0', padding - 15, canvas.height - padding + 15);

        // Demand Vector Line (D)
        ctx.beginPath();
        ctx.strokeStyle = '#2563eb';
        ctx.lineWidth = 3;
        ctx.moveTo(getX(Qe - slopeD * (60 - We)), getY(60));
        ctx.lineTo(getX(Qe - slopeD * (15 - We)), getY(15));
        ctx.stroke();
        ctx.fillText('D', getX(Qe - slopeD * (15 - We)) + 5, getY(15));

        // Supply Vector Line (S)
        ctx.beginPath();
        ctx.strokeStyle = '#ea580c';
        ctx.lineWidth = 3;
        ctx.moveTo(getX(Qe + slopeS * (15 - We)), getY(15));
        ctx.lineTo(getX(Qe + slopeS * (60 - We)), getY(60));
        ctx.stroke();
        ctx.fillText('S', getX(Qe + slopeS * (60 - We)) + 5, getY(60));

        // Equilibrium Dotted Tracking References
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

        // Imposed Legislative Wage Limit Line
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
            // Draw Projection Tracks
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

            // Excess Quantity Points Nodes
            ctx.beginPath();
            ctx.fillStyle = '#ef4444';
            ctx.arc(getX(Qd), getY(W_floor), 5, 0, 2 * Math.PI);
            ctx.arc(getX(Qs), getY(W_floor), 5, 0, 2 * Math.PI);
            ctx.fill();
        }
    }

    // Interactive clicking behavior to synchronize historical rows to the graph model
    historyTableRows.forEach(row => {
        row.addEventListener('click', () => {
            const targetWage = row.getAttribute('data-wage');
            wageSlider.value = targetWage;
            drawMarket();
        });
    });

    wageSlider.addEventListener('input', drawMarket);
    elasticitySelect.addEventListener('change', drawMarket);
    drawMarket();
</script>

</body>
</html>
