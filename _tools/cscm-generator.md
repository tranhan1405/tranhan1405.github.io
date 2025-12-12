
---
title: "CSCM Material Generator"
excerpt: "Generate LS-DYNA CSCM material model files for concrete simulations"
collection: tools
permalink: /tools/cscm-generator/
date: 2024-12-12
---

<style>
    .cscm-container {
        background: linear-gradient(135deg, #0f172a 0%, #1e3a8a 50%, #0f172a 100%);
        min-height: 100vh;
        padding: 2rem;
        margin: -2rem -2rem -2rem -2rem;
    }
    .glass {
        background: rgba(255, 255, 255, 0.1);
        backdrop-filter: blur(10px);
        border: 1px solid rgba(255, 255, 255, 0.2);
    }
    .cscm-input {
        width: 100%;
        padding: 0.75rem 1rem;
        border-radius: 0.5rem;
        background: rgba(255, 255, 255, 0.2);
        border: 1px solid rgba(255, 255, 255, 0.3);
        color: white;
        font-size: 1.125rem;
    }
    .cscm-input:focus {
        outline: none;
        box-shadow: 0 0 0 2px #60a5fa;
    }
    .cscm-input::placeholder {
        color: rgba(255, 255, 255, 0.5);
    }
    .cscm-input.error {
        border-color: #ef4444;
        box-shadow: 0 0 0 2px rgba(239, 68, 68, 0.3);
    }
    .error-message {
        color: #fca5a5;
        font-size: 0.875rem;
        margin-top: 0.5rem;
        display: none;
    }
    .error-message.show {
        display: block;
    }
    .warning-message {
        color: #fcd34d;
        font-size: 0.875rem;
        margin-top: 0.5rem;
        display: none;
    }
    .warning-message.show {
        display: block;
    }
    .btn-generate {
        width: 100%;
        background: linear-gradient(to right, #3b82f6, #2563eb);
        color: white;
        font-weight: 600;
        padding: 1rem;
        border-radius: 0.5rem;
        border: none;
        font-size: 1.125rem;
        cursor: pointer;
        transition: all 0.3s;
    }
    .btn-generate:hover:not(:disabled) {
        background: linear-gradient(to right, #2563eb, #1d4ed8);
        transform: scale(1.02);
    }
    .btn-generate:disabled {
        opacity: 0.5;
        cursor: not-allowed;
    }
    .btn-download {
        display: inline-flex;
        align-items: center;
        gap: 0.5rem;
        background: #10b981;
        color: white;
        font-weight: 600;
        padding: 0.75rem 1.5rem;
        border-radius: 0.5rem;
        border: none;
        cursor: pointer;
        transition: all 0.3s;
    }
    .btn-download:hover {
        background: #059669;
    }
    .preset-btn {
        padding: 0.75rem 1.5rem;
        border-radius: 0.5rem;
        border: 2px solid rgba(255, 255, 255, 0.3);
        background: rgba(255, 255, 255, 0.1);
        color: white;
        font-weight: 600;
        cursor: pointer;
        transition: all 0.3s;
    }
    .preset-btn:hover {
        background: rgba(255, 255, 255, 0.2);
        border-color: #60a5fa;
        transform: translateY(-2px);
    }
    .preset-btn.active {
        background: #3b82f6;
        border-color: #3b82f6;
    }
    .history-item {
        background: rgba(255, 255, 255, 0.05);
        border: 1px solid rgba(255, 255, 255, 0.1);
        border-radius: 0.5rem;
        padding: 1rem;
        margin-bottom: 0.75rem;
        cursor: pointer;
        transition: all 0.3s;
    }
    .history-item:hover {
        background: rgba(255, 255, 255, 0.1);
        border-color: #60a5fa;
    }
    .history-time {
        color: #93c5fd;
        font-size: 0.75rem;
    }
    .history-params {
        color: white;
        font-weight: 600;
        margin-top: 0.25rem;
    }
    .param-card {
        background: rgba(255, 255, 255, 0.05);
        border-radius: 0.5rem;
        padding: 1rem;
        border: 1px solid rgba(255, 255, 255, 0.1);
    }
    .param-label {
        color: #93c5fd;
        font-size: 0.875rem;
        margin-bottom: 0.25rem;
    }
    .param-value {
        color: white;
        font-size: 1.25rem;
        font-weight: 600;
    }
    .preview-box {
        background: rgba(15, 23, 42, 0.7);
        border-radius: 0.5rem;
        padding: 1rem;
        border: 1px solid rgba(255, 255, 255, 0.1);
        max-height: 16rem;
        overflow-y: auto;
    }
    .preview-text {
        color: #86efac;
        font-size: 0.75rem;
        font-family: monospace;
        white-space: pre;
        overflow-x: auto;
    }
    .clear-history {
        background: rgba(239, 68, 68, 0.2);
        border: 1px solid rgba(239, 68, 68, 0.4);
        color: #fca5a5;
        padding: 0.5rem 1rem;
        border-radius: 0.5rem;
        font-size: 0.875rem;
        cursor: pointer;
        transition: all 0.3s;
    }
    .clear-history:hover {
        background: rgba(239, 68, 68, 0.3);
    }
    @media (max-width: 768px) {
        .cscm-container {
            padding: 1rem;
            margin: -1rem;
        }
    }
</style>

<div class="cscm-container">
    <div style="max-width: 80rem; margin: 0 auto; display: grid; grid-template-columns: 1fr 300px; gap: 1.5rem;">
        
        <!-- Main Content -->
        <div>
            <!-- Header -->
            <div class="glass" style="border-radius: 1rem; padding: 2rem; margin-bottom: 1.5rem; box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);">
                <div style="display: flex; align-items: center; gap: 1rem; margin-bottom: 0.5rem;">
                    <svg style="width: 2.5rem; height: 2.5rem; color: #60a5fa;" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"/>
                    </svg>
                    <h1 style="font-size: 2.25rem; font-weight: 700; color: white;">LS-DYNA CSCM</h1>
                </div>
                <p style="color: #93c5fd; font-size: 1.125rem;">Material Model Generator Pro</p>
            </div>

            <!-- Presets -->
            <div class="glass" style="border-radius: 1rem; padding: 2rem; margin-bottom: 1.5rem; box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);">
                <h2 style="font-size: 1.5rem; font-weight: 600; color: white; margin-bottom: 1rem;">Quick Presets</h2>
                <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(120px, 1fr)); gap: 0.75rem;">
                    <button class="preset-btn" onclick="loadPreset(20, 16)">C20/25</button>
                    <button class="preset-btn" onclick="loadPreset(25, 16)">C25/30</button>
                    <button class="preset-btn" onclick="loadPreset(30, 16)">C30/37</button>
                    <button class="preset-btn" onclick="loadPreset(35, 20)">C35/45</button>
                    <button class="preset-btn" onclick="loadPreset(40, 20)">C40/50</button>
                    <button class="preset-btn" onclick="loadPreset(45, 20)">C45/55</button>
                    <button class="preset-btn" onclick="loadPreset(50, 25)">C50/60</button>
                </div>
                <p style="color: #93c5fd; font-size: 0.875rem; margin-top: 1rem;">
                    ℹ️ Quick presets based on Eurocode 2 concrete grades with recommended aggregate sizes
                </p>
            </div>

            <!-- Input Form -->
            <div class="glass" style="border-radius: 1rem; padding: 2rem; margin-bottom: 1.5rem; box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);">
                <h2 style="font-size: 1.5rem; font-weight: 600; color: white; margin-bottom: 1.5rem;">Custom Parameters</h2>
                
                <div style="display: flex; flex-direction: column; gap: 1.5rem;">
                    <div>
                        <label style="display: block; color: white; margin-bottom: 0.5rem; font-size: 1.125rem; font-weight: 500;">
                            Compressive Strength (fc) [MPa] *
                        </label>
                        <input
                            type="number"
                            id="fc"
                            value="30"
                            class="cscm-input"
                            placeholder="Enter fc in MPa"
                            step="0.1"
                            oninput="validateInputs()"
                        />
                        <p class="error-message" id="fc-error">⚠️ Compressive strength must be between 10-100 MPa</p>
                        <p class="warning-message" id="fc-warning">⚠️ Values outside 20-80 MPa range may have reduced accuracy</p>
                        <p style="color: #93c5fd; font-size: 0.875rem; margin-top: 0.5rem;">Typical range: 20-80 MPa (Recommended: 25-50 MPa)</p>
                    </div>

                    <div>
                        <label style="display: block; color: white; margin-bottom: 0.5rem; font-size: 1.125rem; font-weight: 500;">
                            Maximum Aggregate Size (dmax) [mm] *
                        </label>
                        <input
                            type="number"
                            id="dmax"
                            value="16"
                            class="cscm-input"
                            placeholder="Enter dmax in mm"
                            step="1"
                            oninput="validateInputs()"
                        />
                        <p class="error-message" id="dmax-error">⚠️ Aggregate size must be between 4-40 mm</p>
                        <p class="warning-message" id="dmax-warning">⚠️ Common sizes: 8, 16, 20, 25, 32 mm</p>
                        <p style="color: #93c5fd; font-size: 0.875rem; margin-top: 0.5rem;">Standard sizes: 8, 16, 20, 25, 32 mm (Default: 16 mm)</p>
                    </div>

                    <button onclick="generateKFile()" class="btn-generate" id="generate-btn">
                        Generate Material File
                    </button>
                </div>
            </div>

            <!-- Results -->
            <div id="results" class="glass" style="border-radius: 1rem; padding: 2rem; box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5); display: none;">
                <div style="display: flex; align-items: center; justify-content: space-between; margin-bottom: 1.5rem; flex-wrap: wrap; gap: 1rem;">
                    <h2 style="font-size: 1.5rem; font-weight: 600; color: white;">Generated File</h2>
                    <button onclick="downloadFile()" class="btn-download">
                        <svg style="width: 1.25rem; height: 1.25rem;" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"/>
                        </svg>
                        Download .k File
                    </button>
                </div>

                <div style="background: rgba(15, 23, 42, 0.5); border-radius: 0.5rem; padding: 1rem; margin-bottom: 1.5rem; border: 1px solid rgba(255, 255, 255, 0.1);">
                    <p id="filename" style="color: #93c5fd; font-family: monospace; font-size: 0.875rem;"></p>
                </div>

                <!-- Key Parameters -->
                <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 1rem; margin-bottom: 1.5rem;">
                    <div class="param-card">
                        <p class="param-label">Shear Modulus (G)</p>
                        <p id="param-g" class="param-value"></p>
                    </div>
                    <div class="param-card">
                        <p class="param-label">Bulk Modulus (K)</p>
                        <p id="param-k" class="param-value"></p>
                    </div>
                    <div class="param-card">
                        <p class="param-label">Fracture Energy (GF)</p>
                        <p id="param-gf" class="param-value"></p>
                    </div>
                    <div class="param-card">
                        <p class="param-label">Alpha Parameter</p>
                        <p id="param-alpha" class="param-value"></p>
                    </div>
                </div>

                <!-- File Preview -->
                <div class="preview-box">
                    <p style="color: #93c5fd; font-size: 0.875rem; margin-bottom: 0.5rem; font-weight: 600;">File Preview:</p>
                    <pre id="preview" class="preview-text"></pre>
                </div>
            </div>

            <!-- Info Box -->
            <div style="background: rgba(59, 130, 246, 0.1); backdrop-filter: blur(10px); border-radius: 1rem; padding: 1.5rem; margin-top: 1.5rem; border: 1px solid rgba(96, 165, 250, 0.3);">
                <div style="display: flex; align-items: start; gap: 0.75rem;">
                    <svg style="width: 1.5rem; height: 1.5rem; color: #60a5fa; flex-shrink: 0; margin-top: 0.25rem;" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                    </svg>
                    <div>
                        <h3 style="color: white; font-weight: 600; margin-bottom: 0.5rem;">About CSCM Material Model</h3>
                        <p style="color: #93c5fd; font-size: 0.875rem; line-height: 1.6;">
                            The Continuous Surface Cap Model (CSCM) is used in LS-DYNA for simulating concrete and rock materials under dynamic loading conditions. This tool automatically generates material cards based on your concrete's compressive strength and aggregate size.
                        </p>
                    </div>
                </div>
            </div>
        </div>

        <!-- History Sidebar -->
        <div>
            <div class="glass" style="border-radius: 1rem; padding: 1.5rem; box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5); position: sticky; top: 2rem;">
                <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 1rem;">
                    <h3 style="color: white; font-weight: 600; font-size: 1.25rem;">History</h3>
                    <button class="clear-history" onclick="clearHistory()" id="clear-btn" style="display: none;">Clear</button>
                </div>
                <div id="history-list" style="max-height: 600px; overflow-y: auto;">
                    <p style="color: #93c5fd; font-size: 0.875rem; text-align: center; padding: 2rem 0;">No history yet. Generate your first file!</p>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    let currentContent = '';
    let currentFilename = '';
    const MAX_HISTORY = 10;

    // Load history on page load
    window.addEventListener('load', loadHistory);

    function validateInputs() {
        const fc = parseFloat(document.getElementById('fc').value);
        const dmax = parseFloat(document.getElementById('dmax').value);
        
        let isValid = true;
        
        // Validate fc
        const fcInput = document.getElementById('fc');
        const fcError = document.getElementById('fc-error');
        const fcWarning = document.getElementById('fc-warning');
        
        if (isNaN(fc) || fc < 10 || fc > 100) {
            fcInput.classList.add('error');
            fcError.classList.add('show');
            fcWarning.classList.remove('show');
            isValid = false;
        } else {
            fcInput.classList.remove('error');
            fcError.classList.remove('show');
            if (fc < 20 || fc > 80) {
                fcWarning.classList.add('show');
            } else {
                fcWarning.classList.remove('show');
            }
        }
        
        // Validate dmax
        const dmaxInput = document.getElementById('dmax');
        const dmaxError = document.getElementById('dmax-error');
        const dmaxWarning = document.getElementById('dmax-warning');
        
        if (isNaN(dmax) || dmax < 4 || dmax > 40) {
            dmaxInput.classList.add('error');
            dmaxError.classList.add('show');
            dmaxWarning.classList.remove('show');
            isValid = false;
        } else {
            dmaxInput.classList.remove('error');
            dmaxError.classList.remove('show');
            const standardSizes = [8, 16, 20, 25, 32];
            if (!standardSizes.includes(dmax)) {
                dmaxWarning.classList.add('show');
            } else {
                dmaxWarning.classList.remove('show');
            }
        }
        
        // Enable/disable generate button
        const generateBtn = document.getElementById('generate-btn');
        generateBtn.disabled = !isValid;
        
        return isValid;
    }

    function loadPreset(fc, dmax) {
        document.getElementById('fc').value = fc;
        document.getElementById('dmax').value = dmax;
        validateInputs();
        
        // Visual feedback
        document.querySelectorAll('.preset-btn').forEach(btn => btn.classList.remove('active'));
        event.target.classList.add('active');
        setTimeout(() => event.target.classList.remove('active'), 1000);
    }

    function calculateFractureEnergy(fc, dmax) {
        const deltaF = 8;
        const fcm = fc + deltaF;
        const fcm0 = 10;
        const GF0 = 0.021 + 5.357e-4 * dmax;
        const GF = GF0 * Math.pow(fcm / fcm0, 0.7);
        return { GF0, GF };
    }

    function calculateModulus(fc) {
        const Ec0 = 21.5e3;
        const deltaF = 8;
        const fcm = fc + deltaF;
        const fcm0 = 10;
        const poissonRatio = 0.2;
        const E = Ec0 * Math.pow((fcm + deltaF) / fcm0, 1/3);
        const G = E / (2 * (1 + poissonRatio));
        const K = E / (3 * (1 - 2 * poissonRatio));
        return { G, K };
    }

    function calculateAlphaBeta(fc) {
        const alpha = 13.9846 * Math.exp(fc / 68.8756) - 13.8981;
        const theta = 0.3533 - 3.3294e-4 * fc - 3.8182e-6 * (fc ** 2);
        const lamda = 3.6657 * Math.exp(fc / 39.9363) - 4.7092;
        const beta = 18.17791 * (fc ** -1.7163);

        const alpha1 = 0.82;
        const theta1 = 0.0;
        const lamda1 = 0.24;
        const beta1 = 0.33565 * (fc ** -0.95383);

        const alpha2 = 0.76;
        const theta2 = 0.0;
        const lamda2 = 0.26;
        const beta2 = 0.285 * (fc ** -0.94843);

        return { alpha, theta, lamda, beta, alpha1, theta1, lamda1, beta1, alpha2, theta2, lamda2, beta2 };
    }

    function calculateAdditionalParameters(fc) {
        const fpsi = fc * 145.038;
        const eta0c = 1.2772337e-11 * fpsi ** 2 - 1.0613722e-7 * fpsi + 3.203497e-4;
        const nc = 0.78;
        const eta0t = 8.0614774e-13 * fc ** 2 - 9.77736719e-10 * fc + 5.0752351e-5;
        const nt = 0.48;
        const overc = 1.309663e-2 * fc ** 2 - 0.3927659 * fc + 21.45;
        const overt = 1.309663e-2 * fc ** 2 - 0.3927659 * fc + 21.45;
        const srate = 1.0;
        const repow = 1.0;
        return { eta0c, nc, eta0t, nt, overc, overt, srate, repow };
    }

    function calculateRXD(fc) {
        const R = 4.45994 * Math.exp(-fc / 11.51679) + 1.95358;
        const XD = 17.087 + 1.892 * fc;
        return { R, XD };
    }

    function formatNum(num, decimals) {
        return num.toFixed(decimals).padStart(10);
    }

    async function saveToHistory(fc, dmax, filename) {
        try {
            const historyKey = 'cscm-history';
            let history = [];
            
            try {
                const result = await window.storage.get(historyKey);
                if (result && result.value) {
                    history = JSON.parse(result.value);
                }
            } catch (e) {
                // Key doesn't exist yet
            }
            
            const entry = {
                fc: fc,
                dmax: dmax,
                filename: filename,
                timestamp: new Date().toISOString()
            };
            
            history.unshift(entry);
            if (history.length > MAX_HISTORY) {
                history = history.slice(0, MAX_HISTORY);
            }
            
            await window.storage.set(historyKey, JSON.stringify(history));
            loadHistory();
        } catch (error) {
            console.log('Could not save to history:', error);
        }
    }

    async function loadHistory() {
        try {
            const result = await window.storage.get('cscm-history');
            if (!result || !result.value) {
                return;
            }
            
            const history = JSON.parse(result.value);
            const historyList = document.getElementById('history-list');
            const clearBtn = document.getElementById('clear-btn');
            
            if (history.length === 0) {
                historyList.innerHTML = '<p style="color: #93c5fd; font-size: 0.875rem; text-align: center; padding: 2rem 0;">No history yet. Generate your first file!</p>';
                clearBtn.style.display = 'none';
                return;
            }
            
            clearBtn.style.display = 'block';
            historyList.innerHTML = history.map((entry, index) => {
                const date = new Date(entry.timestamp);
                const timeStr = date.toLocaleString('en-US', { 
                    month: 'short', 
                    day: 'numeric',
                    hour: '2-digit', 
                    minute: '2-digit'
                });
                
                return `
                    <div class="history-item" onclick="loadFromHistory(${index})">
                        <div class="history-time">${timeStr}</div>
                        <div class="history-params">fc=${entry.fc} MPa, dmax=${entry.dmax} mm</div>
                    </div>
                `;
            }).join('');
        } catch (error) {
            console.log('Could not load history:', error);
        }
    }

    async function loadFromHistory(index) {
        try {
            const result = await window.storage.get('cscm-history');
            if (!result || !result.value) return;
            
            const history = JSON.parse(result.value);
            const entry = history[index];
            
            document.getElementById('fc').value = entry.fc;
            document.getElementById('dmax').value = entry.dmax;
            validateInputs();
            
            // Scroll to top
            window.scrollTo({ top: 0, behavior: 'smooth' });
        } catch (error) {
            console.log('Could not load from history:', error);
        }
    }

    async function clearHistory() {
        if (confirm('Are you sure you want to clear all history?')) {
            try {
                await window.storage.delete('cscm-history');
                loadHistory();
            } catch (error) {
                console.log('Could not clear history:', error);
            }
        }
    }

    function generateKFile() {
        if (!validateInputs()) {
            return;
        }

        const fcVal = parseFloat(document.getElementById('fc').value);
        const dmaxVal = parseFloat(document.getElementById('dmax').value);

        const { GF0, GF } = calculateFractureEnergy(fcVal, dmaxVal);
        const { G, K } = calculateModulus(fcVal);
        const { alpha, theta, lamda, beta, alpha1, theta1, lamda1, beta1, alpha2, theta2, lamda2, beta2 } = calculateAlphaBeta(fcVal);
        const { R, XD } = calculateRXD(fcVal);
        const { eta0c, nc, eta0t, nt, overc, overt, srate, repow } = calculateAdditionalParameters(fcVal);

        const GFC = 100 * GF;
        const GFT = GF;
        const GFS = GF;
        const B = 100;
        const D = 0.1;
        const PWRC = 5;
        const PWRT = 1;
        const PMOD = 0;
        const mid = Math.floor(Math.random() * 900) + 100;
        const ro = 0.0023;

        const now = new Date();
        const dateStr = now.toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' });

        const content = `$# LS-DYNA Keyword file created by LS-PrePost(R) V4.8.17 - 24Jun2021
$# Created on ${dateStr}
*KEYWORD
*TITLE
$#                                                                         title
LS-DYNA keyword deck by LS-PrePost
*MAT_CSCM_TITLE
MAT_CSCM_${fcVal}MPa
$#     mid        ro     nplot     incre     irate     erode     recov   itretrc
${mid.toString().padStart(10)}${formatNum(ro, 4)}${(1).toString().padStart(10)}${formatNum(0.0, 1)}${(0).toString().padStart(10)}${formatNum(1.05, 2)}${formatNum(0.0, 1)}${(0).toString().padStart(10)}
$#    pred
${formatNum(0.0, 1)}
$#       g         k     alpha     theta     lamda      beta        nh        ch
