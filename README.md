<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced HKDSE Economics Minimum Wage Lab & Diagnostic Center</title>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: #f5f7fa; color: #333; padding: 20px; display: flex; justify-content: center; }
        .container { max-width: 1200px; width: 100%; background: white; padding: 30px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.05); }
        h1 { color: #1e293b; margin-top: 0; margin-bottom: 5px; font-size: 26px; }
        .subtitle { color: #64748b; margin-bottom: 25px; font-size: 14px; }
        
        /* Interactive Inputs Grid */
        .control-panel { background: #f8fafc; border: 1px solid #e2e8f0; padding: 20px; border-radius: 8px; margin-bottom: 20px; display: grid; grid-template-columns: 1.2fr 1fr 1fr; gap: 20px; }
        label { font-weight: bold; display: block; margin-bottom: 6px; color: #475569; font-size: 13px; }
        input[type="range"] { width: 100%; cursor: pointer; }
        select { width: 100%; padding: 8px; border-radius: 4px; border: 1px solid #cbd5e1; font-size: 14px; background: white; }
        
        .badge { display: inline-block; padding: 6px 12px; border-radius: 20px; font-weight: bold; margin-bottom: 15px; font-size: 13px; }
        .effective { background-color: #fee2e2; color: #991b1b; }
        .ineffective { background-color: #f1f5f9; color: #475569; }
        
        /* Stats Panel */
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
        
        /* Archive Section */
        .history-section { border-top: 2px dashed #e2e8f0; padding-top: 25px; margin-top: 10px; margin-bottom: 30px; }
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
        .timeline-table tbody tr:hover { background: #fee2e2; }
        .timeline-table tr.selected-row { background: #fee2e2 !important; font-weight: bold; border-left: 4px solid #dc2626; }
        
        .rate-tag { background: #e2e8f0; color: #1e293b; padding: 2px 5px; border-radius: 4px; font-weight: bold; display: inline-block; }
        .change-tag { font-weight: 600; display: block; margin-top: 2px; font-size: 11px; }
        .change-up { color: #15803d; }
        .change-flat { color: #64748b; }
        
        /* Advanced Quiz Framework Style Elements */
        .quiz-section { border-top: 3px double #cbd5e1; padding-top: 30px; margin-top: 20px; }
        .quiz-section h2 { color: #0f172a; font-size: 21px; margin-bottom: 5px; }
        .quiz-desc { font-size: 13.5px; color: #64748b; margin-bottom: 25px; }
        .quiz-card { background: #ffffff; border: 1px solid #e2e8f0; border-radius: 8px; padding: 20px; margin-bottom: 20px; box-shadow: 0 2px 5px rgba(0,0,0,0.02); }
        .q-number { font-weight: bold; color: #dc2626; margin-bottom: 4px; font-size: 14px; }
        .q-text { font-weight: 600; color: #1e293b; margin-bottom: 12px; font-size: 14.5px; line-height: 1.5; }
        .q-options { display: grid; grid-template-columns: 1fr; gap: 8px; margin-bottom: 15px; }
        .opt-row { background: #f8fafc; border: 1px solid #e2e8f0; padding: 10px 14px; border-radius: 6px; font-size: 13.5px; cursor: pointer; transition: all 0.2s; }
        .opt-row:hover { background: #f1f5f9; border-color: #cbd5e1; }
        
        /* Solution Dropdowns and Mini Canvases */
        details { background: #f8fafc; border: 1px solid #cbd5e1; border-radius: 6px; margin-top: 10px; overflow: hidden; }
        summary { font-weight: bold; padding: 10px 14px; cursor: pointer; background: #e2e8f0; color: #334155; font-size: 13px; outline: none; }
        .details-content { padding: 14px; font-size: 13.5px; line-height: 1.6; color: #334155; border-top: 1px solid #cbd5e1; display: flex; flex-direction: row; gap: 20px; align-items: flex-start; }
        .details-text { flex: 1; }
        .quiz-canvas { background: #ffffff; border: 1px solid #cbd5e1; border-radius: 6px; width: 240px; height: 180px; flex-shrink: 0; }
        .ans-pill { display: inline-block; background: #16a34a; color: white; padding: 2px 8px; border-radius: 4px; font-weight: bold; font-size: 12px; margin-bottom: 8px; }
        
        @media (max-width: 768px) {
            .control-panel { grid-template-columns: 1fr; }
            .stats-grid { grid-template-columns: repeat(2, 1fr); }
            .workspace-layout { grid-template-columns: 1fr; }
            .details-content { flex-direction: column; }
            .quiz-canvas { width: 100%; height: 200px; }
        }

        .text-gain { color: #059669; font-weight: bold; }
        .text-loss { color: #dc2626; font-weight: bold; }
        .highlight-concept { font-weight: 600; text-decoration: underline; }
    </style>
</head>
<body>

<div class="container">
    <h1>Advanced HKDSE Economics Minimum Wage Simulator</h1>
    <div class="subtitle">Simulate real-time microeconomic market shocks (Demand & Supply curves shifting) alongside legal price floor thresholds and advanced validation matrices.</div>
    
    <div class="control-panel">
        <div>
            <label for="wageSlider">Statutory Minimum Wage ($/Hr): $<span id="wageVal">43.1</span></label>
            <input type="range" id="wageSlider" min="25" max="60" value="43.1" step="0.1">
            <label for="elasticitySelect" style="margin-top: 12px;">Demand Price Elasticity (E<sub>d</sub>)</label>
            <select id="elasticitySelect">
                <option value="inelastic" selected>Inelastic (Ed &lt; 1)</option>
                <option value="elastic">Elastic (Ed &gt; 1)</option>
            </select>
        </div>
        <div>
            <label for="demandShock">Labor Demand Shock (Curve Shifts)</label>
            <select id="demandShock">
                <option value="none" selected>Constant Demand (D Baseline)</option>
                <option value="increase">Increase (+D) [Economic Expansion]</option>
                <option value="decrease">Decrease (-D) [Industry Structural Recession]</option>
            </select>
        </div>
        <div>
            <label for="supplyShock">Labor Supply Shock (Curve Shifts)</label>
            <select id="supplyShock">
                <option value="none" selected>Constant Supply (S Baseline)</option>
                <option value="increase">Increase (+S) [Workforce/Immigration Inflow]</option>
                <option value="decrease">Decrease (-S) [Aging Demographics Outflow]</option>
            </select>
        </div>
    </div>

    <div id="statusBadge" class="badge effective">Effective Minimum Wage</div>

    <div class="stats-grid">
        <div class="stat-card">
            <div class="stat-title">Actual Employment (Q)</div>
            <div class="stat-num" id="statEmp">100.0</div>
        </div>
        <div class="stat-card">
            <div class="stat-title">Involuntary Unemployment (Surplus)</div>
            <div class="stat-num" id="statUnemp">0.0</div>
        </div>
        <div class="stat-card" id="earningsCard">
            <div class="stat-title">Total Wage Earnings</div>
            <div class="stat-num" id="statEarnings">$4,000</div>
        </div>
        <div class="stat-card" id="dwlCard">
            <div class="stat-title">Deadweight Loss (DWL)</div>
            <div class="stat-num" id="statDwl">$0</div>
        </div>
    </div>

    <div class="workspace-layout">
        <canvas id="marketGraph" width="560" height="420"></canvas>
        
        <div class="analysis-box">
            <h3>HKDSE Market Shock Evaluation</h3>
            <div id="analysisText">Computing metrics...</div>
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
            *Click on any historical milestone row below to automatically update the interactive wage constraint slider line.
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

    <div class="quiz-section">
        <h2>🔥 High-Distinction Diagnostic Exam Suite (HKDSE Level)</h2>
        <div class="quiz-desc">Test your understanding with 5 of the most conceptually challenging multiple-choice questions regarding price floors and dynamic market interference frameworks. Expand each solution to view its dedicated analytical diagram.</div>

        <div class="quiz-card">
            <div class="q-number">QUESTION 1 (Elasticity & Revenue Manipulations)</div>
            <div class="q-text">Suppose an effective minimum wage is already imposed on the low-skilled labor market in Hong Kong. If the government decides to raise this statutory rate further while the price elasticity of labor demand is highly inelastic within the relevant range, which of the following statements is/are CORRECT?<br>
            (1) The involuntary unemployment gap in the market will widen.<br>
            (2) Total wage earnings of the remaining employed workers will increase.<br>
            (3) The deadweight loss to society will decrease because workers enjoy a higher wage rate.</div>
            <div class="q-options">
                <div class="opt-row">A. (1) and (2) only</div>
                <div class="opt-row">B. (1) and (3) only</div>
                <div class="opt-row">C. (2) and (3) only</div>
                <div class="opt-row">D. (1), (2) and (3)</div>
            </div>
            <details data-quiz="1">
                <summary>View Solution & Embedded Diagram</summary>
                <div class="details-content">
                    <div class="details-text">
                        <div class="ans-pill">CORRECT ANSWER: A</div><br>
                        <b>Economic Explanation:</b><br>
                        • <b>Statement (1) is correct:</b> As seen in the diagram, raising the price floor from W<sub>min1</sub> to W<sub>min2</sub> moves the quantity demanded leftwards (Q<sub>d1</sub> to Q<sub>d2</sub>) and quantity supplied rightwards (Q<sub>s1</sub> to Q<sub>s2</sub>), expanding the involuntary unemployment gap.<br>
                        • <b>Statement (2) is correct:</b> Because demand is highly inelastic (steep curve), the gain in wage revenue from the remaining workers (Area Gain) is significantly larger than the revenue loss from laid-off workers (Area Loss), increasing total wage earnings.<br>
                        • <b>Statement (3) is incorrect:</b> The deadweight loss triangle expands outwards as actual market transactions are further restricted from equilibrium.
                    </div>
                    <canvas id="quizCanvas1" class="quiz-canvas" width="240" height="180"></canvas>
                </div>
            </details>
        </div>

        <div class="quiz-card">
            <div class="q-number">QUESTION 2 (Simultaneous Shocks & Effectiveness Thresholds)</div>
            <div class="q-text">An effective statutory minimum wage is established in an industry. Simultaneously, a structural economic recession decreases the consumer demand for the industry's output, while a concurrent revision in immigration policy results in a massive influx of low-skilled foreign workers. Holding the legal wage rate constant, how will the involuntary labor unemployment space change?</div>
            <div class="q-options">
                <div class="opt-row">A. It will decrease because firms lay off workers to preserve baseline capital.</div>
                <div class="opt-row">B. It will increase because labor demand shifts left while labor supply shifts right.</div>
                <div class="opt-row">C. It will remain unchanged because the statutory rate acts as a structural anchor.</div>
                <div class="opt-row">D. It may increase or decrease, depending entirely on the price elasticity of supply.</div>
            </div>
            <details data-quiz="2">
                <summary>View Solution & Embedded Diagram</summary>
                <div class="details-content">
                    <div class="details-text">
                        <div class="ans-pill">CORRECT ANSWER: B</div><br>
                        <b>Economic Explanation:</b><br>
                        • The product market recession reduces derived labor demand, causing a leftward shift from D<sub>1</sub> to D<sub>2</sub>. This decreases quantity demanded at the minimum wage line to Q<sub>d2</sub>.<br>
                        • The immigration influx shifts labor supply rightward from S<sub>1</sub> to S<sub>2</sub>, extending quantity supplied at the minimum wage line to Q<sub>s2</sub>.<br>
                        • As illustrated, the structural surplus gap widens substantially from the original gap (Q<sub>s1</sub> - Q<sub>d1</sub>) to the new expanded gap (Q<sub>s2</sub> - Q<sub>d2</sub>).
                    </div>
                    <canvas id="quizCanvas2" class="quiz-canvas" width="240" height="180"></canvas>
                </div>
            </details>
        </div>

        <div class="quiz-card">
            <div class="q-number">QUESTION 3 (Cross-Market Interdependence)</div>
            <div class="q-text">Assume low-skilled human cashiers and automated self-service kiosks are strong substitutes in the retail sector. If an effective statutory minimum wage is introduced for human cashiers, what will be the resulting market impact on automated self-service kiosks?</div>
            <div class="q-options">
                <div class="opt-row">A. Demand for kiosks will increase, leading to a higher equilibrium price for kiosks.</div>
                <div class="opt-row">B. Supply of kiosks will decrease as manufacturers reallocate capital back to human training.</div>
                <div class="opt-row">C. Quantity transacted of kiosks will decrease due to the deadweight loss generated in the human cashier market.</div>
                <div class="opt-row">D. Equilibrium price of kiosks will drop as kiosks become relatively more expensive to operate.</div>
            </div>
            <details data-quiz="3">
                <summary>View Solution & Embedded Diagram</summary>
                <div class="details-content">
                    <div class="details-text">
                        <div class="ans-pill">CORRECT ANSWER: A</div><br>
                        <b>Economic Explanation:</b><br>
                        • Imposing an effective minimum wage increases the hiring cost of human cashiers, making them relatively more expensive.<br>
                        • Because automated kiosks are substitutes, firms reallocate capital away from human workers. This directly increases the market demand for self-service kiosks, shifting the demand curve rightward from D<sub>1</sub> to D<sub>2</sub>.<br>
                        • As the kiosk market diagram illustrates, this shift drives up both the equilibrium price (P<sub>e1</sub> to P<sub>e2</sub>) and quantity of kiosks transacted.
                    </div>
                    <canvas id="quizCanvas3" class="quiz-canvas" width="240" height="180"></canvas>
                </div>
            </details>
        </div>

        <div class="quiz-card">
            <div class="q-number">QUESTION 4 (Ineffective vs Effective Boundary Shifts)</div>
            <div class="q-text">The government maintains a statutory minimum wage line at $40. Due to a sudden global technological surge, the marginal productivity of labor rises substantially, causing a massive rightward shift in the market labor demand curve (D). If the new natural market clearing wage shifts from $38 to $45, what happens to the actual market wage rate and deadweight loss?</div>
            <div class="q-options">
                <div class="opt-row">A. The wage rate remains at $40; deadweight loss increases.</div>
                <div class="opt-row">B. The wage rate rises to $45; deadweight loss drops to zero.</div>
                <div class="opt-row">C. The wage rate rises to $45; deadweight loss increases because the gap from $40 has expanded.</div>
                <div class="opt-row">D. The market fails to clear because transactions above $40 are blocked by price floor rules.</div>
            </div>
            <details data-quiz="4">
                <summary>View Solution & Embedded Diagram</summary>
                <div class="details-content">
                    <div class="details-text">
                        <div class="ans-pill">CORRECT ANSWER: B</div><br>
                        <b>Economic Explanation:</b><br>
                        • Initially, the baseline equilibrium wage ($38) was lower than the statutory minimum wage floor ($40), rendering the regulation effective and binding.<br>
                        • The rightward shift in labor demand to D<sub>2</sub> relocates the market intersection up to a natural clearing level of $45.<br>
                        • Because the statutory floor ($40) is now below the equilibrium price ($45), the regulation turns completely ineffective. Market prices clear naturally at $45, eliminating allocative inefficiencies and dropping deadweight loss to zero.
                    </div>
                    <canvas id="quizCanvas4" class="quiz-canvas" width="240" height="180"></canvas>
                </div>
            </details>
        </div>

        <div class="quiz-card">
            <div class="q-number">QUESTION 5 (Perfect Elasticity Vector Constraints)</div>
            <div class="q-text">If a labor market has a perfectly vertical (perfectly inelastic) labor supply curve and an effective statutory minimum wage is introduced, which of the following is an absolute certainty regarding market metrics?</div>
            <div class="q-options">
                <div class="opt-row">A. Deadweight loss will be maximized because the entire supply curve is blocked.</div>
                <div class="opt-row">B. The size of the involuntary unemployment gap will equal zero.</div>
                <div class="opt-row">C. The actual quantity of labor employed is determined entirely by the labor demand curve at the minimum wage rate.</div>
                <div class="opt-row">D. Total wage earnings in the market will decrease regardless of the elasticity of demand.</div>
            </div>
            <details data-quiz="5">
                <summary>View Solution & Embedded Diagram</summary>
                <div class="details-content">
                    <div class="details-text">
                        <div class="ans-pill">CORRECT ANSWER: C</div><br>
                        <b>Economic Explanation:</b><br>
                        • When an effective price control forces the market out of equilibrium, the transacted quantity is governed by the short side of the market. Since W<sub>min</sub> exceeds equilibrium, quantity demanded is smaller than quantity supplied.<br>
                        • As shown in the diagram, even though supply is completely vertical (constant pool), firms restrict hiring to the demand curve boundary. The actual volume of employment drops down to match Q<sub>d</sub> exactly, confirming statement C.<br>
                        • Involuntary unemployment exists as the gap between the vertical supply line and Q<sub>d</sub>.
                    </div>
                    <canvas id="quizCanvas5" class="quiz-canvas" width="240" height="180"></canvas>
                </div>
            </details>
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

    const We_orig = 40.0;
    const Qe_orig = 100;

    function drawMarket() {
        const W_floor = parseFloat(wageSlider.value);
        const elasticity = elasticitySelect.value;
        const dShock = demandShock.value;
        const sShock = supplyShock.value;
        
        wageVal.innerText = W_floor.toFixed(1);

        let dShift = 0;
        if (dShock === 'increase') dShift = 30;
        if (dShock === 'decrease') dShift = -30;

        let sShift = 0;
        if (sShock === 'increase') sShift = 30;
        if (sShock === 'decrease') sShift = -30;

        let slopeD = elasticity === 'inelastic' ? 1.5 : 4.0;
        let slopeS = 2.0;

        const We_new = We_orig + (dShift - sShift) / (slopeD + slopeS);
        const Qe_new = Qe_orig + dShift - slopeD * (We_new - We_orig);

        const isEffective = W_floor > We_new;
        
        let Qd = Qe_new;
        let Qs = Qe_new;

        if (isEffective) {
            Qd = Qe_new - slopeD * (W_floor - We_new);
            Qs = Qe_new + slopeS * (W_floor - We_new);
        }

        if (Qd < 0) Qd = 0;

        const actualEmp = isEffective ? Qd : Qe_new;
        const surplus = isEffective ? Math.max(0, Qs - Qd) : 0;
        const totalEarnings = (isEffective ? W_floor : We_new) * actualEmp;
        const baselineRevenue = We_new * Qe_new;

        const dwl = isEffective ? 0.5 * (W_floor - (We_new - (Qe_new - Qd) / slopeS)) * (Qe_new - Qd) : 0;

        statEmp.innerText = actualEmp.toFixed(1);
        statUnemp.innerText = surplus.toFixed(1);
        statEarnings.innerText = '$' + Math.round(totalEarnings);
        statDwl.innerText = isEffective ? '$' + Math.round(dwl) : '$0';
        
        let shockSummary = "";
        if (dShock !== 'none' || sShock !== 'none') {
            shockSummary = `<b>Market Shocks Triggered:</b> Dynamic interaction forces shifted the equilibrium clearing wage point from $${We_orig.toFixed(1)} to <b>$${We_new.toFixed(1)}</b>.<br><br>`;
        }

        if (isEffective) {
            statusBadge.innerText = "Effective Minimum Wage (Binding Floor)";
            statusBadge.className = "badge effective";
            dwlCard.className = "stat-card loss";
            
            if (totalEarnings > baselineRevenue) {
                earningsCard.className = "stat-card gain";
                analysisText.innerHTML = `${shockSummary}
                    <b>Policy Status:</b> Effective ($${W_floor.toFixed(1)} &gt; Current Dynamic Equilibrium $${We_new.toFixed(1)})<br><br>
                    <b>Total Labor Expenditure Outcome:</b><br>
                    Because labor demand is <span class="text-gain">Inelastic</span>, the percentage wage adjustment outpaces the percentage layoff response.<br>
                    • Green Geometric Box &gt; Red Geometric Box.<br>
                    • Total earnings <span class="text-gain">increased</span> to $${Math.round(totalEarnings)}.<br><br>
                    <b>Market Efficiency:</b> Creates involuntary unemployment of <b>${surplus.toFixed(1)} workers</b>, leading to an efficiency <b>Deadweight Loss</b> of $${Math.round(dwl)}.
                `;
            } else {
                earningsCard.className = "stat-card loss";
                analysisText.innerHTML = `${shockSummary}
                    <b>Policy Status:</b> Effective ($${W_floor.toFixed(1)} &gt; Current Dynamic Equilibrium $${We_new.toFixed(1)})<br><br>
                    <b>Total Labor Expenditure Outcome:</b><br>
                    Because labor demand is <span class="text-loss">Elastic</span>, firms rapidly downscale staffing levels to offset regulatory costs.<br>
                    • Red Geometric Box &gt; Green Geometric Box.<br>
                    • Total earnings <span class="text-loss">decreased</span> to $${Math.round(totalEarnings)}.<br><br>
                    <b>Market Efficiency:</b> Severe workforce scaling yields an unemployment surplus of <b>${surplus.toFixed(1)} workers</b>. DWL hit $${Math.round(dwl)}.
                `;
            }
        } else {
            statusBadge.innerText = "Ineffective Minimum Wage (Non-Binding Floor)";
            statusBadge.className = "badge ineffective";
            earningsCard.className = "stat-card";
            dwlCard.className = "stat-card";
            analysisText.innerHTML = `${shockSummary}
                <b>Policy Status:</b> Ineffective ($${W_floor.toFixed(1)} &le; Current Dynamic Equilibrium $${We_new.toFixed(1)})<br><br>
                <b>Outcome Analysis:</b><br>
                The minimum wage constraint is below the dynamic equilibrium rate. Market forces pull coordinates up to equilibrium clearing price <b>$${We_new.toFixed(1)}</b>.<br><br>
                • Employment clears naturally at <b>${Qe_new.toFixed(1)}</b>.<br>
                • Total Expenditure is fixed at baseline <b>$${Math.round(baselineRevenue)}</b> (Shaded gray block).<br>
                • No job position displacement or efficiency loss is introduced.
            `;
        }

        historyTableRows.forEach(row => {
            const rowWage = parseFloat(row.getAttribute('data-wage'));
            if (Math.abs(W_floor - rowWage) < 0.05) {
                row.classList.add('selected-row');
            } else {
                row.classList.remove('selected-row');
            }
        });

        ctx.clearRect(0, 0, canvas.width, canvas.height);
        const padding = 60;
        const graphWidth = canvas.width - padding * 2;
        const graphHeight = canvas.height - padding * 2;

        function getX(q) { return padding + (q / 180) * graphWidth; }
        function getY(w) { return canvas.height - padding - (w / 75) * graphHeight; }

        if (isEffective) {
            ctx.fillStyle = 'rgba(16, 185, 129, 0.18)';
            ctx.fillRect(getX(0), getY(W_floor), getX(Qd) - getX(0), getY(We_new) - getY(W_floor));
            ctx.fillStyle = 'rgba(239, 68, 68, 0.18)';
            ctx.fillRect(getX(Qd), getY(We_new), getX(Qe_new) - getX(Qd), getY(0) - getY(We_new));
            ctx.fillStyle = 'rgba(249, 115, 22, 0.25)';
            ctx.beginPath();
            ctx.moveTo(getX(Qd), getY(W_floor));
            ctx.lineTo(getX(Qe_new), getY(We_new));
            ctx.lineTo(getX(Qd), getY(We_new - (Qe_new - Qd) / slopeS));
            ctx.closePath();
            ctx.fill();
        } else {
            ctx.fillStyle = 'rgba(148, 163, 184, 0.15)';
            ctx.fillRect(getX(0), getY(We_new), getX(Qe_new) - getX(0), getY(0) - getY(We_new));
        }

        ctx.beginPath(); ctx.strokeStyle = '#334155'; ctx.lineWidth = 2;
        ctx.moveTo(padding, padding); ctx.lineTo(padding, canvas.height - padding);
        ctx.lineTo(canvas.width - padding, canvas.height - padding); ctx.stroke();

        ctx.fillStyle = '#334155'; ctx.font = 'bold 11px sans-serif';
        ctx.fillText('Wage Rate ($)', padding - 50, padding - 15);
        ctx.fillText('Quantity of Labor (Q)', canvas.width - padding - 60, canvas.height - padding + 40);

        function drawCurve(x1, y1, x2, y2, color, label, isDashed = false) {
            ctx.beginPath(); ctx.strokeStyle = color; ctx.lineWidth = isDashed ? 1.5 : 3;
            if (isDashed) ctx.setLineDash([4, 4]);
            ctx.moveTo(getX(x1), getY(y1)); ctx.lineTo(getX(x2), getY(y2)); ctx.stroke();
            ctx.setLineDash([]); ctx.fillStyle = color; ctx.font = 'bold 12px sans-serif';
            ctx.fillText(label, getX(x2) + 5, getY(y2) + 4);
        }

        if (dShock !== 'none') {
            drawCurve(Qe_orig - slopeD * (65 - We_orig), 65, Qe_orig - slopeD * (15 - We_orig), 15, '#93c5fd', 'D1', true);
        }
        if (sShock !== 'none') {
            drawCurve(Qe_orig + slopeS * (15 - We_orig), 15, Qe_orig + slopeS * (65 - We_orig), 65, '#fdbb2d', 'S1', true);
        }

        const d_center = Qe_orig + dShift;
        drawCurve(d_center - slopeD * (65 - We_orig), 65, d_center - slopeD * (15 - We_orig), 15, '#2563eb', dShock !== 'none' ? 'D2' : 'D');

        const s_center = Qe_orig + sShift;
        drawCurve(s_center + slopeS * (15 - We_orig), 15, s_center + slopeS * (65 - We_orig), 65, '#ea580c', sShock !== 'none' ? 'S2' : 'S');

        ctx.setLineDash([3, 3]); ctx.strokeStyle = '#94a3b8'; ctx.lineWidth = 1;
        ctx.beginPath(); ctx.moveTo(getX(0), getY(We_new)); ctx.lineTo(getX(Qe_new), getY(We_new)); ctx.stroke();
        ctx.beginPath(); ctx.moveTo(getX(Qe_new), getY(0)); ctx.lineTo(getX(Qe_new), getY(We_new)); ctx.stroke();
        ctx.setLineDash([]);
        
        ctx.fillStyle = '#475569'; ctx.font = '10px sans-serif';
        ctx.fillText(`W_e ($${We_new.toFixed(1)})`, padding - 55, getY(We_new) + 4);
        ctx.fillText(`Q_e (${Qe_new.toFixed(1)})`, getX(Qe_new) - 20, canvas.height - padding + 18);

        ctx.beginPath();
        ctx.strokeStyle = isEffective ? '#dc2626' : '#64748b';
        ctx.lineWidth = isEffective ? 2.5 : 1.5;
        ctx.moveTo(getX(0), getY(W_floor)); ctx.lineTo(getX(165), getY(W_floor)); ctx.stroke();
        
        ctx.fillStyle = isEffective ? '#dc2626' : '#64748b'; ctx.font = 'bold 11px sans-serif';
        ctx.fillText('W_min ($' + W_floor.toFixed(1) + ')', getX(165) - 95, getY(W_floor) - 6);

        if (isEffective) {
            ctx.setLineDash([2, 2]); ctx.strokeStyle = '#dc2626'; ctx.lineWidth = 1;
            ctx.beginPath(); ctx.moveTo(getX(Qd), getY(W_floor)); ctx.lineTo(getX(Qd), getY(0)); ctx.stroke();
            ctx.beginPath(); ctx.moveTo(getX(Qs), getY(W_floor)); ctx.lineTo(getX(Qs), getY(0)); ctx.stroke();
            ctx.setLineDash([]);
            
            ctx.fillStyle = '#dc2626'; ctx.font = '10px sans-serif';
            ctx.fillText('Q_d', getX(Qd) - 8, canvas.height - padding + 18);
            ctx.fillText('Q_s', getX(Qs) - 8, canvas.height - padding + 18);

            ctx.beginPath(); ctx.fillStyle = '#ef4444';
            ctx.arc(getX(Qd), getY(W_floor), 4.5, 0, 2 * Math.PI);
            ctx.arc(getX(Qs), getY(W_floor), 4.5, 0, 2 * Math.PI);
            ctx.fill();
        }
    }

    // UNIQUE RENDERING SUITE FOR EMBEDDED DIAGRAMS
    function drawBaseAxes(c, p) {
        c.beginPath(); c.strokeStyle = '#475569'; c.lineWidth = 1.5;
        c.moveTo(p, p); c.lineTo(p, 180 - p); c.lineTo(240 - p, 180 - p); c.stroke();
    }

    function renderQuizDiagrams() {
        const p = 25;
        const w = 240, h = 180;
        const gW = w - p * 2, gH = h - p * 2;

        const getX = (v) => p + (v / 100) * gW;
        const getY = (v) => h - p - (v / 100) * gH;

        // --- DIAGRAM 1: ELASTICITY & PREMIUM ---
        const c1 = document.getElementById('quizCanvas1').getContext('2d');
        drawBaseAxes(c1, p);
        // Inelastic Demand (Steep)
        c1.beginPath(); c1.strokeStyle = '#2563eb'; c1.lineWidth = 2;
        c1.moveTo(getX(20), getY(85)); c1.lineTo(getX(60), getY(15)); c1.stroke();
        c1.fillStyle = '#2563eb'; c1.fillText('D', getX(62), getY(15));
        // Supply
        c1.beginPath(); c1.strokeStyle = '#ea580c'; c1.moveTo(getX(20), getY(20)); c1.lineTo(getX(70), getY(75)); c1.stroke();
        // Floor lines
        c1.strokeStyle = '#dc2626'; c1.setLineDash([2,2]);
        c1.beginPath(); c1.moveTo(p, getY(55)); c1.lineTo(getX(80), getY(55)); c1.stroke(); // Floor 1
        c1.beginPath(); c1.moveTo(p, getY(70)); c1.lineTo(getX(80), getY(70)); c1.stroke(); // Floor 2
        c1.setLineDash([]); c1.fillStyle = '#dc2626'; c1.font = '9px sans-serif';
        c1.fillText('W_min2', w - p - 35, getY(73));
        c1.fillText('W_min1', w - p - 35, getY(51));
        // Fill Gain Area Green
        c1.fillStyle = 'rgba(16, 185, 129, 0.3)';
        c1.fillRect(getX(29), getY(70), getX(37)-getX(29), getY(55)-getY(70));
        c1.fillStyle = '#059669'; c1.fillText('Gain', getX(10), getY(64));

        // --- DIAGRAM 2: DUAL SHIFT ---
        const c2 = document.getElementById('quizCanvas2').getContext('2d');
        drawBaseAxes(c2, p);
        // Demand 1 and Demand 2
        c2.lineWidth = 1.5;
        c2.beginPath(); c2.strokeStyle = '#93c5fd'; c2.moveTo(getX(20), getY(80)); c2.lineTo(getX(70), getY(30)); c2.stroke();
        c2.beginPath(); c2.strokeStyle = '#2563eb'; c2.moveTo(getX(5), getY(70)); c2.lineTo(getX(55), getY(20)); c2.stroke();
        c2.fillText('D2', getX(57), getY(20));
        // Supply 1 and Supply 2
        c2.beginPath(); c2.strokeStyle = '#fdbb2d'; c2.moveTo(getX(15), getY(25)); c2.lineTo(getX(65), getY(75)); c2.stroke();
        c2.beginPath(); c2.strokeStyle = '#ea580c'; c2.moveTo(getX(35), getY(25)); c2.lineTo(getX(85), getY(75)); c2.stroke();
        c2.fillText('S2', getX(87), getY(75));
        // Constant Wage line
        c2.strokeStyle = '#dc2626'; c2.beginPath(); c2.moveTo(p, getY(60)); c2.lineTo(w-p, getY(60)); c2.stroke();
        c2.fillStyle = '#dc2626'; c2.fillText('W_min', p+5, getY(64));
        // Mark gap widening
        c2.fillStyle = 'rgba(239, 68, 68, 0.2)';
        c2.fillRect(getX(15), getY(60), getX(70)-getX(15), 6);

        // --- DIAGRAM 3: CROSS SECTOR INTERDEPENDENCE ---
        const c3 = document.getElementById('quizCanvas3').getContext('2d');
        drawBaseAxes(c3, p);
        c3.beginPath(); c3.strokeStyle = '#2563eb'; c3.setLineDash([2,2]);
        c3.moveTo(getX(15), getY(65)); c3.lineTo(getX(65), getY(15)); c3.stroke(); // D1
        c3.setLineDash([]);
        c3.beginPath(); c3.moveTo(getX(30), getY(75)); c3.lineTo(getX(80), getY(25)); c3.stroke(); // D2
        c3.fillText('D2', getX(82), getY(25));
        c3.beginPath(); c3.strokeStyle = '#ea580c'; c3.moveTo(getX(15), getY(20)); c3.lineTo(getX(75), getY(80)); c3.stroke();
        c3.fillText('S', getX(77), getY(80));
        // Project Price increases
        c3.strokeStyle = '#94a3b8'; c3.setLineDash([2,2]);
        c3.beginPath(); c3.moveTo(p, getY(46)); c3.lineTo(getX(41), getY(46)); c3.stroke();
        c3.beginPath(); c3.moveTo(p, getY(56)); c3.lineTo(getX(51), getY(56)); c3.stroke();
        c3.fillStyle = '#1e293b'; c3.fillText('P2', p-16, getY(56));

        // --- DIAGRAM 4: BOUNDARY SHIFT INEFFECTIVE ---
        const c4 = document.getElementById('quizCanvas4').getContext('2d');
        drawBaseAxes(c4, p);
        c4.beginPath(); c4.strokeStyle = '#93c5fd'; c4.setLineDash([2,2]);
        c4.moveTo(getX(15), getY(70)); c4.lineTo(getX(65), getY(20)); c4.stroke(); // D1
        c4.setLineDash([]);
        c4.beginPath(); c4.strokeStyle = '#2563eb'; c4.moveTo(getX(35), getY(85)); c4.lineTo(getX(85), getY(35)); c4.stroke(); // D2
        c4.fillText('D2', getX(87), getY(35));
        c4.beginPath(); c4.strokeStyle = '#ea580c'; c4.moveTo(getX(15), getY(25)); c4.lineTo(getX(75), getY(85)); c4.stroke();
        // Floor Line below new intersection
        c4.strokeStyle = '#64748b'; c4.beginPath(); c4.moveTo(p, getY(42)); c4.lineTo(w-p, getY(42)); c4.stroke();
        c4.fillStyle = '#64748b'; c4.fillText('W_floor ($40)', p+5, getY(46));
        c4.fillStyle = '#2563eb'; c4.fillText('E2 ($45)', getX(58), getY(63));

        // --- DIAGRAM 5: VERTICAL SUPPLY ---
        const c5 = document.getElementById('quizCanvas5').getContext('2d');
        drawBaseAxes(c5, p);
        // Vertical Supply Curve
        c5.beginPath(); c5.strokeStyle = '#ea580c'; c5.lineWidth = 3;
        c5.moveTo(getX(50), getY(15)); c5.lineTo(getX(50), getY(85)); c5.stroke();
        c5.fillText('S', getX(48), getY(89));
        // Demand
        c5.beginPath(); c5.strokeStyle = '#2563eb'; c5.lineWidth = 2;
        c5.moveTo(getX(15), getY(80)); c5.lineTo(getX(75), getY(20)); c5.stroke();
        // Floor Line
        c5.strokeStyle = '#dc2626'; c5.beginPath(); c2.lineWidth = 1.5;
        c5.moveTo(p, getY(62)); c5.lineTo(w-p, getY(62)); c5.stroke();
        // Intersection point Qd
        c5.fillStyle = '#dc2626'; c5.beginPath(); c5.arc(getX(33), getY(62), 4, 0, 2*Math.PI); c5.fill();
        c5.fillText('Q_d determines employment', getX(5), getY(72));
    }

    historyTableRows.forEach(row => {
        row.addEventListener('click', () => {
            wageSlider.value = row.getAttribute('data-wage');
            drawMarket();
        });
    });

    wageSlider.addEventListener('input', drawMarket);
    elasticitySelect.addEventListener('change', drawMarket);
    demandShock.addEventListener('change', drawMarket);
    supplyShock.addEventListener('change', drawMarket);
    
    // Initial Boot frameworks
    drawMarket();
    renderQuizDiagrams();
</script>

</body>
</html>
