<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced HKDSE Economics Minimum Wage Lab</title>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: #f5f7fa; color: #333; padding: 20px; display: flex; justify-content: center; }
        .container { max-width: 1200px; width: 100%; background: white; padding: 30px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.05); }
        h1 { color: #1e293b; margin-top: 0; margin-bottom: 5px; font-size: 26px; }
        .subtitle { color: #64748b; margin-bottom: 25px; font-size: 14px; }
        
        /* Advanced Control Grid */
        .control-panel { background: #f8fafc; border: 1px solid #e2e8f0; padding: 20px; border-radius: 8px; margin-bottom: 20px; display: grid; grid-template-columns: 1.2fr 1fr 1fr; gap: 15px; }
        label { font-weight: bold; display: block; margin-bottom: 6px; color: #475569; font-size: 13px; }
        input[type="range"] { width: 100%; cursor: pointer; }
        select { width: 100%; padding: 8px; border-radius: 4px; border: 1px solid #cbd5e1; font-size: 14px; background: white; }
        
        .badge { display: inline-block; padding: 6px 12px; border-radius: 20px; font-weight: bold; margin-bottom: 15px; font-size: 13px; }
        .effective { background-color: #fee2e2; color: #991b1b; }
        .ineffective { background-color: #f1f5f9; color: #475569; }
        
        .stats-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 15px; margin-bottom: 20px; }
        .stat-card { background: #f1f5f9; padding: 12px; border-radius: 8px; text-align: center; border-left: 4px solid #cbd5e1; }
        .stat-card.gain { border-left-color: #10b981; }
        .stat-card.loss { border-left-color: #ef4444; }
        .stat-title { font-size: 12px; color: #64748b; font-weight: 500; }
        .stat-num { font-size: 20px; font-weight: bold; margin-top: 4px; color: #0f172a; }
        
        .workspace-layout { display: grid; grid-template-columns: 1.2fr 1fr; gap: 25px; margin-bottom: 30px; align-items: start; }
        canvas { background: #ffffff; border: 1px solid #e2e8f0; width: 100%; height: auto; display: block; border-radius: 8px; }
        .analysis-box { background: #f8fafc; border: 1px solid #e2e8f0; padding: 20px; border-radius: 8px; font-size: 13.5px; line-height: 1.6; height: 100%; box-sizing: border-box; }
        .analysis-box h3 { margin-top: 0; color: #1e293b; font-size: 15px; border-bottom: 1px solid #e2e8f0; padding-bottom: 8px; margin-bottom: 12px; }
        
        /* History & Value Base */
        .history-section { border-top: 2px dashed #e2e8f0; padding-top: 25px; margin-top: 10px; }
        .history-section h2 { color: #0f172a; font-size: 19px; margin-top: 0; margin-bottom: 15px; }
        
        .values-education-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 20px; }
        .value-card { padding: 15px; border-radius: 8px; font-size: 13px; line-height: 1.5; border-left: 4px solid; }
        .value-card.edu { background: #f0fdf4; border-left-color: #16a34a; color: #14532d; }
        .value-card.sec { background: #f0f9ff; border-left-color: #0284c7; color: #0c4a6e; }
        .value-card h4 { margin: 0 0 6px 0; font-size: 14px; font-weight: bold; }
        
        .table-wrapper { overflow-x: auto; }
        .timeline-table { width: 100%; border-collapse: collapse; text-align: left; font-size: 12.5px; line-height: 1.4; }
        .timeline-table th { background: #f1f5f9; color: #334155; padding: 10px; font-weight: 600; border-bottom: 2px solid #cbd5e1; }
        .timeline-table td { padding: 10px; border-bottom: 1px solid #e2e8f0; vertical-align: top; }
        .timeline-table tbody tr { cursor: pointer; transition: background 0.2s; }
        .timeline-table tbody tr:hover { background: #fcf1f1; }
        .timeline-table tr.selected-row { background: #fee2e2 !important; font-weight: bold; border-left: 4px solid #dc2626; }
        
        .rate-tag { background: #e2e8f0; color: #1e293b; padding: 2px 5px; border-radius: 4px; font-weight: bold; display: inline-block; }
        .change-tag { font-weight: 600; display: block; margin-top: 2px; font-size: 11px; }
        .change-up { color: #15803d; }
        .change-flat { color: #64748b; }
        
        .text-gain { color: #059669; font-weight: bold; }
        .text-loss { color: #dc2626; font-weight: bold; }
        .highlight-concept { font-weight: 600; text-decoration: underline; }
    </style>
</head>
<body>

<div class="container">
    <h1>Advanced HKDSE Economics Minimum Wage Simulator</h1>
    <div class="subtitle">An advanced simulation playground tracking simultaneous market shocks (Demand & Supply curves shifting) combined with legislative price controls.</div>
    
    <div class="control-panel">
        <div>
            <label for="wageSlider">Statutory Minimum Wage ($/Hr): $<span id="wageVal">43.1</span></label>
            <input type="range" id="wageSlider" min="25" max="60" value="43.1" step="0.1">
            <label for="elasticitySelect" style="margin-top: 10px;">Demand Price Elasticity (E<sub>d</sub>)</label>
            <select id="elasticitySelect">
                <option value="inelastic" selected>Inelastic (Ed < 1)</option>
                <option value="elastic">Elastic (Ed > 1)</option>
            </select>
        </div>
        <div>
            <label for="demandShock">Labor Demand Shock (Curve Position)</label>
            <select id="demandShock">
                <option value="none" selected>Constant Demand (D1 Baseline)</option>
                <option value="increase">Increase (+D) [e.g., Economic Boom]</option>
                <option value="decrease">Decrease (-D) [e.g., Industry Recession]</option>
            </select>
        </div>
        <div>
            <label for="supplyShock">Labor Supply Shock (Curve Position)</label>
            <select id="supplyShock">
                <option value="none" selected>Constant Supply (S1 Baseline)</option>
                <option value="increase">Increase (+S) [e.g., Immigration Inflow]</option>
                <option value="decrease">Decrease (-S) [e.g., Aging Workforce]</option>
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
            <div class="stat-title">Total Wage Earnings</div>
            <div class="stat-num" id="statEarnings">$4,043</div>
        </div>
        <div class="stat-card" id="dwlCard">
            <div class="stat-title">Deadweight Loss (DWL)</div>
            <div class="stat-num" id="statDwl">$10</div>
        </div>
    </div>

    <div class="workspace-layout">
        <canvas id="marketGraph" width="560" height="420"></canvas>
        
        <div class="analysis-box">
            <h3>HKDSE Market Shock Evaluation</h3>
            <div id="analysisText">Computing algebraic structural shifts...</div>
        </div>
    </div>

    <div class="history-section">
        <h2>Socio-Economic Rationale & Statutory History Archive</h2>
        
        <div class="values-education-grid">
            <div class="value-card edu">
                <h4>🌱 Values Education Perspective</h4>
                Focuses on the core values of <b>Human Dignity, Mutual Help, and Social Justice</b>. By establishing a legal wage floor, the policy ensures grassroot workers receive equitable compensation that respects their work contribution, fosters self-reliance, and enhances families' quality of life.
            </div>
            <div class="value-card sec">
                <h4>🛡️ National Security (Economic & Social Security)</h4>
                Extreme income polarization can trigger social frictions and labor unrest. Safeguarding low-income workers protects <b>Social Stability</b> and <b>Economic Security</b>, ensuring the operational resilience of core domestic industries and mitigating systemic risks.
            </div>
        </div>

        <h3>📈 Statutory Minimum Wage Rate Timeline (2011–2026)</h3>
        <p style="font-size: 13px; color: #64748b; margin-top: -8px; margin-bottom: 15px;">
            *Click on any historical row below to sync the minimum wage rate line directly onto the graphical model workspace above.
        </p>

        <div class="table-wrapper">
            <table class="timeline-table" id="historyTable">
                <thead>
                    <tr>
                        <th style="width: 120px;">Effective Date</th>
                        <th style="width: 110px;">Hourly Rate</th>
                        <th style="width: 250px;">Underlying Policy Motive & Value Concept</th>
                        <th>Context / Economic Environment</th>
                    </tr>
                </thead>
                <tbody>
                    <tr data-wage="43.1">
                        <td><b>May 1, 2026</b></td>
                        <td><span class="rate-tag">$43.10</span><span class="change-tag change-up">+$1.00 (+2.4%)</span></td>
                        <td><span class="highlight-concept">Institutional Stability</span>: Optimizes income predictability through a formulaic annual review mechanism.</td>
                        <td>Current approved statutory rate managed via the modern formula framework.</td>
                    </tr>
                    <tr data-wage="42.1">
                        <td>May 1, 2025</td>
                        <td><span class="rate-tag">$42.10</span><span class="change-tag change-up">+$2.10 (+5.2%)</span></td>
                        <td><span class="highlight-concept">Proactive Governance</span>: Transitions away from biennial delays to better match living costs.</td>
                        <td>First initialization layer using the shifting annual formula-based review metrics.</td>
                    </tr>
                    <tr data-wage="40.0">
                        <td>May 1, 2023</td>
                        <td><span class="rate-tag">$40.00</span><span class="change-tag change-up">+$2.50 (+6.7%)</span></td>
                        <td><span class="highlight-concept">Social Justice</span>: Resumes purchasing power catch-up for frontline households post-epidemic.</td>
                        <td><b>Our Original Baseline Market Equilibrium ($40).</b> Increases restarted as post-pandemic retail sectors adjusted.</td>
                    </tr>
                    <tr data-wage="37.5">
                        <td>May 1, 2021</td>
                        <td><span class="rate-tag">$37.50</span><span class="change-tag change-flat">$0.00 (Frozen)</span></td>
                        <td><span class="highlight-concept">Economic Security</span>: Avoids business bankruptcies and severe job losses during extreme crisis.</td>
                        <td>First historical freeze. Held stable to minimize layoffs during structural COVID downturns.</td>
                    </tr>
                    <tr data-wage="37.5">
                        <td>May 1, 2019</td>
                        <td><span class="rate-tag">$37.50</span><span class="change-tag change-up">+$3.00 (+8.7%)</span></td>
                        <td><span class="highlight-concept">Sharing Fruit of Growth</span>: Distributes wealth gained during prosperous cycles to the workforce.</td>
                        <td>Strong macroeconomic conditions allowed for a notable dollar adjustment.</td>
                    </tr>
                    <tr data-wage="34.5">
                        <td>May 1, 2017</td>
                        <td><span class="rate-tag">$34.50</span><span class="change-tag change-up">+$2.00 (+6.2%)</span></td>
                        <td><span class="highlight-concept">Human Dignity</span>: Preserves a baseline living standard against ongoing rent and food cost growth.</td>
                        <td>Steady baseline updates aiming to secure low-income frontline household purchasing power.</td>
                    </tr>
                    <tr data-wage="32.5">
                        <td>May 1, 2015</td>
                        <td><span class="rate-tag">$32.50</span><span class="change-tag change-up">+$2.50 (+8.3%)</span></td>
                        <td><span class="highlight-concept">Mutual Help</span>: Balances enterprise affordability with workers' baseline needs during steady growth.</td>
                        <td>Supported by continuous positive growth trends across domestic logistics and service industries.</td>
                    </tr>
                    <tr data-wage="30.0">
                        <td>May 1, 2013</td>
                        <td><span class="rate-tag">$30.00</span><span class="change-tag change-up">+$2.00 (+7.1%)</span></td>
                        <td><span class="highlight-concept">Inflation Mitigation</span>: Prevents low-wage exploitation as consumer prices escalate.</td>
                        <td>First cyclical updates under the original biennial assessment strategy to counteract inflation.</td>
                    </tr>
                    <tr data-wage="28.0">
                        <td>May 1, 2011</td>
                        <td><span class="rate-tag">$28.00</span><span class="change-tag change-flat">Initial Rate</span></td>
                        <td><span class="highlight-concept">Social Harmony</span>: Eradicates working poverty to minimize structural socio-political friction.</td>
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
    const demandShock = document.getElementById('demandShock');
    const supplyShock = document.getElementById('supplyShock');
    
    const statusBadge = document.getElementById('statusBadge');
    const statEmp = document.getElementById('statEmp');
    const statUnemp = document.getElementById('statUnemp');
    const statEarnings = document.getElementById('statEarnings');
    const statDwl = document.getElementById('statDwl');
    const earningsCard = document.getElementById('earningsCard');
    const dwlCard = document.getElementById('dwlCard');
    const analysisText = document.getElementById('analysisText');
    const historyTableRows = document.querySelectorAll('#historyTable tbody tr');

    // Baseline Parameters
    const We_orig = 40.0;
    const Qe_orig = 100;

    function drawMarket() {
        const W_floor = parseFloat(wageSlider.value);
        const elasticity = elasticitySelect.value;
        const dShock = demandShock.value;
        const sShock = supplyShock.value;
        
        wageVal.innerText = W_floor.toFixed(1);

        // Calculate Shift Shifts mathematically
        let dShift = 0;
        if (dShock === 'increase') dShift = 25;
        if (dShock === 'decrease') dShift = -25;

        let sShift = 0;
        if (sShock === 'increase') sShift = 25;
        if (sShock === 'decrease') sShift = -25;

        // Curve constants
        let slopeD = elasticity === 'inelastic' ? 2.0 : 4.5;
        let slopeS = 2.0;

        // Compute new theoretical equilibrium (Intersection of shifted curves)
        // Qd = Qe_orig + dShift - slopeD * (W - We_orig)
        // Qs = Qe_orig + sShift + slopeS * (W - We_orig)
        // Set Qd = Qs => W_new_eq = We_orig + (dShift - sShift) / (slopeD + slopeS)
        const We_new = We_orig + (dShift - sShift) / (slopeD + slopeS);
        const Qe_new = Qe_orig + dShift - slopeD * (We_new - We_orig);

        const isEffective = W_floor > We_new;
        
        let Qd = Qe_new;
        let Qs = Qe_new;

        if (isEffective) {
            Qd = Qe_new - slopeD * (W_floor - We_new);
            Qs = Qe_new + slopeS * (W_floor - We_new);
        }

        const actualEmp = isEffective ? Qd : Qe_new;
        const surplus = isEffective ? (Qs - Qd) : 0;
        const totalEarnings = (isEffective ? W_floor : We_new) * actualEmp;
        const baselineRevenue = We_new * Qe_new;

        // Deadweight Loss calculation via transaction height mismatch
        const dwl = isEffective ? 0.5 * (W_floor - (We_new - (Qe_new - Qd) / slopeS)) * (Qe_new - Qd) : 0;

        // Update indicators
        statEmp.innerText = actualEmp.toFixed(1);
        statUnemp.innerText = surplus.toFixed(1);
        statEarnings.innerText = '$' + Math.round(totalEarnings);
        statDwl.innerText = isEffective ? '$' + Math.round(dwl) : '$0';
        
        // Formulate Advanced Explanatory Commentary
        let shockSummary = "";
        if (dShock !== 'none' || sShock !== 'none') {
            shockSummary = `<b>Market Environment Shock:</b> the equilibrium wage point shifted from $${We_orig.toFixed(1)} to <b>$${We_new.toFixed(1)}</b> due to structural forces.<br><br>`;
        }

        if (isEffective) {
            statusBadge.innerText = "Effective Minimum Wage (Binding Floor)";
            statusBadge.className = "badge effective";
            dwlCard.className = "stat-card loss";
            
            if (totalEarnings > baselineRevenue) {
                earningsCard.className = "stat-card gain";
                analysisText.innerHTML = `${shockSummary}
                    <b>Policy Status:</b> Effective ($${W_floor.toFixed(1)} &gt; Current New Eq $${We_new.toFixed(1)})<br><br>
                    <b>Total Earnings Elasticity Effect:</b><br>
                    Because labor demand is <span class="text-gain">Inelastic</span> within this spectrum, the wage premium percentage surpasses the layoff adjustment percentage.<br>
                    • Green Shaded Box &gt; Red Shaded Box.
                    • Aggregate earnings <span class="text-gain">rose</span> to $${Math.round(totalEarnings)}.<br><br>
                    <b>Market Surplus:</b> creates an involuntary unemployment gap of <b>${surplus.toFixed(1)} workers</b>, inducing a <b>Deadweight Loss</b> of $${Math.round(dwl)}.
                `;
            } else {
                earningsCard.className = "stat-card loss";
                analysisText.innerHTML = `${shockSummary}
                    <b>Policy Status:</b> Effective ($${W_floor.toFixed(1)} &gt; Current New Eq $${We_new.toFixed(1)})<br><br>
                    <b>Total Earnings Elasticity Effect:</b><br>
                    Because labor demand is <span class="text-loss">Elastic</span>, firms shed operational hours dramatically to mitigate regulatory expenditure overheads.<br>
                    • Red Shaded Box &gt; Green Shaded Box.
                    • Aggregate earnings <span class="text-loss">dropped</span> to $${Math.round(totalEarnings)}.<br><br>
                    <b>Market Surplus:</b> massive headcounts are displaced. Labor surplus hits <b>${surplus.toFixed(1)} units</b>. DWL expands to $${Math.round(dwl)}.
                `;
            }
        } else {
            statusBadge.innerText = "Ineffective Minimum Wage (Non-Binding Floor)";
            statusBadge.className = "badge ineffective";
            earningsCard.className = "stat-card";
            dwlCard.className = "stat-card";
            analysisText.innerHTML = `${shockSummary}
                <b>Policy Status:</b> Ineffective ($${W_floor.toFixed(1)} &le; Current New Eq $${We_new.toFixed(1)})<br><br>
                <b>Outcome Analysis:</b><br>
                The wage regulation lies at or below the dynamic market-clearing price. Natural supply/demand forces dominate, clearing transactions natively at <b>$${We_new.toFixed(1)}</b>.<br><br>
                • Employment operates smoothly at new equilibrium <b>${Qe_new.toFixed(1)}</b>.<br>
                • Total Wage Revenue stays fixed at baseline <b>$${Math.round(baselineRevenue)}</b> (Shaded constant gray block).<br>
                • No involuntary displacement or Deadweight Loss occurs.
            `;
        }

        // Table highlight engine
        historyTableRows.forEach(row => {
            const rowWage = parseFloat(row.getAttribute('data-wage'));
            if (Math.abs(W_floor - rowWage) < 0.05) {
                row.classList.add('selected-row');
            } else {
                row.classList.remove('selected-row');
            }
        });

        // --- GRAPH CANVAS ENGINE ---
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        const padding = 60;
        const graphWidth = canvas.width - padding * 2;
        const graphHeight = canvas.height - padding * 2;

        function getX(q) { return padding + (q / 180) * graphWidth; }
        function getY(w) { return canvas.height - padding - (w / 75) * graphHeight; }

        // Core Area Geometry Fills
        if (isEffective) {
            // Gain Box (Green)
            ctx.fillStyle = 'rgba(16, 185, 129, 0.18)';
            ctx.fillRect(getX(0), getY(W_floor), getX(Qd) - getX(0), getY(We_new) - getY(W_floor));
            // Loss Box (Red)
            ctx.fillStyle = 'rgba(239, 68, 68, 0.18)';
            ctx.fillRect(getX(Qd), getY(We_new), getX(Qe_new) - getX(Qd), getY(0) - getY(We_new));
            // Deadweight Loss Triangle (Orange)
            ctx.fillStyle = 'rgba(249, 115, 22, 0.25)';
            ctx.beginPath();
            ctx.moveTo(getX(Qd), getY(W_floor));
            ctx.lineTo(getX(Qe_new), getY(We_new));
            ctx.lineTo(getX(Qd), getY(We_new - (Qe_new - Qd) / slopeS));
            ctx.closePath();
            ctx.fill();
        } else {
            // Constant Equilibrium Total Expenditure Frame (Gray)
            ctx.fillStyle = 'rgba(148, 163, 184, 0.15)';
            ctx.fillRect(getX(0), getY(We_new), getX(Qe_new) - getX(0), getY(0) - getY(We_new));
        }

        // Render Axes
        ctx.beginPath(); ctx.strokeStyle = '#334155'; ctx.lineWidth = 2;
        ctx.moveTo(padding, padding); ctx.lineTo(padding, canvas.height - padding);
        ctx.lineTo(canvas.width - padding, canvas.height - padding); ctx.stroke();

        ctx.fillStyle = '#334155'; ctx.font = 'bold 11px sans-serif';
        ctx.fillText('Wage Rate ($)', padding - 50, padding - 15);
        ctx.fillText('Quantity of Labor (Q)', canvas.width - padding - 60, canvas.height - padding + 40);

        // --- DRAW CURVE FUNCTIONS ---
        function drawCurve(x1, y1, x2, y2, color, label, isDashed = false) {
            ctx.beginPath(); ctx.strokeStyle = color; ctx.lineWidth = isDashed ? 1.5 : 3;
            if (isDashed) ctx.setLineDash([4, 4]);
            ctx.moveTo(getX(x1), getY(y1)); ctx.lineTo(getX(x2), getY(y2)); ctx.stroke();
            ctx.setLineDash([]); ctx.fillStyle = color; ctx.font = 'bold 12px sans-serif';
            ctx.fillText(label, getX(x2) + 5, getY(y2) + 4);
        }

        // Render D1 Baseline if shifted
        if (dShock !== 'none') {
            drawCurve(Qe_orig - slopeD*(65-We_orig), 65, Qe_orig - slopeD*(15-We_orig), 15, '#93c5fd', 'D1', true);
        }
        // Render S1 Baseline if shifted
        if (sShock !== 'none') {
            drawCurve(Qe_orig + slopeS*(15-We_orig), 15, Qe_orig + slopeS*(65-We_orig), 65, '#fdbb2d', 'S1', true);
        }

        // Render Active Shifted Demand (D2)
        const d_center = Qe_orig + dShift;
        drawCurve(d_center - slopeD*(65-We_orig), 65, d_center - slopeD*(15-We_orig), 15, '#2563eb', dShock !== 'none' ? 'D2' : 'D');

        // Render Active Shifted Supply (S2)
        const s_center = Qe_orig + sShift;
        drawCurve(s_center + slopeS*(15-We_orig), 15, s_center + slopeS*(65-We_orig), 65, '#ea580c', sShock !== 'none' ? 'S2' : 'S');

        // Render Dynamic Equilibrium Trackers
        ctx.setLineDash([3, 3]); ctx.strokeStyle = '#94a3b8'; ctx.lineWidth = 1;
        ctx.beginPath(); ctx.moveTo(getX(0), getY(We_new)); ctx.lineTo(getX(Qe_new), getY(We_new)); ctx.stroke();
        ctx.beginPath(); ctx.moveTo(getX(Qe_new), getY(0)); ctx.lineTo(getX(Qe_new), getY(We_new)); ctx.stroke();
        ctx.setLineDash([]);
        
        ctx.fillStyle = '#475569'; ctx.font = '10px sans-serif';
        ctx.fillText(`W_e ($${We_new.toFixed(1)})`, padding - 55, getY(We_new) + 4);
        ctx.fillText(`Q_e (${Qe_new.toFixed(1)})`, getX(Qe_new) - 20, canvas.height - padding + 18);

        // Render Legal Minimum Wage Constraint Line
        ctx.beginPath();
        ctx.strokeStyle = isEffective ? '#dc2626' : '#64748b';
        ctx.lineWidth = isEffective ? 2.5 : 1.5;
        ctx.moveTo(getX(0), getY(W_floor)); ctx.lineTo(getX(165), getY(W_floor)); ctx.stroke();
        
        ctx.fillStyle = isEffective ? '#dc2626' : '#64748b'; ctx.font = 'bold 11px sans-serif';
        ctx.fillText('W_min ($' + W_floor.toFixed(1) + ')', getX(165) - 95, getY(W_floor) - 6);

        if (isEffective) {
            // Drop intercept projection dashed strings
            ctx.setLineDash([2, 2]); ctx.strokeStyle = '#dc2626'; ctx.lineWidth = 1;
            ctx.beginPath(); ctx.moveTo(getX(Qd), getY(W_floor)); ctx.lineTo(getX(Qd), getY(0)); ctx.stroke();
            ctx.beginPath(); ctx.moveTo(getX(Qs), getY(W_floor)); ctx.lineTo(getX(Qs), getY(0)); ctx.stroke();
            ctx.setLineDash([]);
            
            ctx.fillStyle = '#dc2626'; ctx.font = '10px sans-serif';
            ctx.fillText('Q_d', getX(Qd) - 8, canvas.height - padding + 18);
            ctx.fillText('Q_s', getX(Qs) - 8, canvas.height - padding + 18);

            // Node Endpoints
            ctx.beginPath(); ctx.fillStyle = '#ef4444';
            ctx.arc(getX(Qd), getY(W_floor), 4.5, 0, 2 * Math.PI);
            ctx.arc(getX(Qs), getY(W_floor), 4.5, 0, 2 * Math.PI);
            ctx.fill();
        }
    }

    // Connect Table click mechanisms
    historyTableRows.forEach(row => {
        row.addEventListener('click', () => {
            wageSlider.value = row.getAttribute('data-wage');
            drawMarket();
        });
    });

    // Handle interactive parameter switches
    wageSlider.addEventListener('input', drawMarket);
    elasticitySelect.addEventListener('change', drawMarket);
    demandShock.addEventListener('change', drawMarket);
    supplyShock.addEventListener('change', drawMarket);
    
    // Initial Boot
    drawMarket();
</script>

</body>
</html>
