---
layout: default
title: CSCM Material Generator
permalink: /tools/cscm/
---

<div id="cscm-app">
  <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LS-DYNA CSCM Material Generator</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body {
            background: linear-gradient(135deg, #0f172a 0%, #1e3a8a 50%, #0f172a 100%);
            min-height: 100vh;
        }
        .glass {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
    </style>
</head>
<body class="p-6">
    <div class="max-w-4xl mx-auto">
        <!-- Header -->
        <div class="glass rounded-2xl p-8 mb-6 shadow-2xl">
            <div class="flex items-center gap-4 mb-2">
                <svg class="w-10 h-10 text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"/>
                </svg>
                <h1 class="text-4xl font-bold text-white">LS-DYNA CSCM</h1>
            </div>
            <p class="text-blue-200 text-lg">Material Model Generator</p>
        </div>

        <!-- Input Form -->
        <div class="glass rounded-2xl p-8 mb-6 shadow-2xl">
            <h2 class="text-2xl font-semibold text-white mb-6">Input Parameters</h2>
            
            <div class="space-y-6">
                <div>
                    <label class="block text-white mb-2 text-lg font-medium">
                        Compressive Strength (fc) [MPa]
                    </label>
                    <input
                        type="number"
                        id="fc"
                        value="30"
                        class="w-full px-4 py-3 rounded-lg bg-white/20 border border-white/30 text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-blue-400 text-lg"
                        placeholder="Enter fc in MPa"
                        step="0.1"
                    />
                    <p class="text-blue-200 text-sm mt-2">Typical range: 20-100 MPa</p>
                </div>

                <div>
                    <label class="block text-white mb-2 text-lg font-medium">
                        Maximum Aggregate Size (dmax) [mm]
                    </label>
                    <input
                        type="number"
                        id="dmax"
                        value="16"
                        class="w-full px-4 py-3 rounded-lg bg-white/20 border border-white/30 text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-blue-400 text-lg"
                        placeholder="Enter dmax in mm"
                        step="1"
                    />
                    <p class="text-blue-200 text-sm mt-2">Default: 16 mm</p>
                </div>

                <button
                    onclick="generateKFile()"
                    class="w-full bg-gradient-to-r from-blue-500 to-blue-600 hover:from-blue-600 hover:to-blue-700 text-white font-semibold py-4 rounded-lg transition-all transform hover:scale-[1.02] shadow-lg text-lg"
                >
                    Generate Material File
                </button>
            </div>
        </div>

        <!-- Results -->
        <div id="results" class="glass rounded-2xl p-8 border shadow-2xl hidden">
            <div class="flex items-center justify-between mb-6">
                <h2 class="text-2xl font-semibold text-white">Generated File</h2>
                <button
                    onclick="downloadFile()"
                    class="flex items-center gap-2 bg-green-500 hover:bg-green-600 text-white font-semibold px-6 py-3 rounded-lg transition-all shadow-lg"
                >
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"/>
                    </svg>
                    Download .k File
                </button>
            </div>

            <div class="bg-slate-900/50 rounded-lg p-4 mb-6 border border-white/10">
                <p id="filename" class="text-blue-200 font-mono text-sm"></p>
            </div>

            <!-- Key Parameters -->
            <div class="grid grid-cols-2 gap-4 mb-6">
                <div class="bg-white/5 rounded-lg p-4 border border-white/10">
                    <p class="text-blue-200 text-sm mb-1">Shear Modulus (G)</p>
                    <p id="param-g" class="text-white text-xl font-semibold"></p>
                </div>
                <div class="bg-white/5 rounded-lg p-4 border border-white/10">
                    <p class="text-blue-200 text-sm mb-1">Bulk Modulus (K)</p>
                    <p id="param-k" class="text-white text-xl font-semibold"></p>
                </div>
                <div class="bg-white/5 rounded-lg p-4 border border-white/10">
                    <p class="text-blue-200 text-sm mb-1">Fracture Energy (GF)</p>
                    <p id="param-gf" class="text-white text-xl font-semibold"></p>
                </div>
                <div class="bg-white/5 rounded-lg p-4 border border-white/10">
                    <p class="text-blue-200 text-sm mb-1">Alpha Parameter</p>
                    <p id="param-alpha" class="text-white text-xl font-semibold"></p>
                </div>
            </div>

            <!-- File Preview -->
            <div class="bg-slate-900/70 rounded-lg p-4 border border-white/10">
                <p class="text-blue-200 text-sm mb-2 font-semibold">File Preview:</p>
                <pre id="preview" class="text-green-300 text-xs font-mono overflow-x-auto max-h-64 overflow-y-auto"></pre>
            </div>
        </div>

        <!-- Info Box -->
        <div class="bg-blue-500/10 glass rounded-2xl p-6 mt-6 border border-blue-400/30">
            <div class="flex items-start gap-3">
                <svg class="w-6 h-6 text-blue-400 flex-shrink-0 mt-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                </svg>
                <div>
                    <h3 class="text-white font-semibold mb-2">About CSCM Material Model</h3>
                    <p class="text-blue-200 text-sm leading-relaxed">
                        The Continuous Surface Cap Model (CSCM) is used in LS-DYNA for simulating concrete and rock materials under dynamic loading conditions. This tool automatically generates material cards based on your concrete's compressive strength and aggregate size.
                    </p>
                </div>
            </div>
        </div>
    </div>

    <script>
        let currentContent = '';
        let currentFilename = '';

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

        function generateKFile() {
            const fcVal = parseFloat(document.getElementById('fc').value);
            const dmaxVal = parseFloat(document.getElementById('dmax').value);

            if (isNaN(fcVal) || fcVal <= 0) {
                alert('Please enter a valid compressive strength (fc > 0)');
                return;
            }

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
${formatNum(G, 1)}${formatNum(K, 1)}${formatNum(alpha, 4)}${formatNum(theta, 7)}${formatNum(lamda, 5)}${formatNum(beta, 7)}${formatNum(0.0, 1)}${formatNum(0.0, 1)}
$#  alpha1    theta1    lamda1     beta1    alpha2    theta2    lamda2     beta2
${formatNum(alpha1, 2)}${formatNum(theta1, 1)}${formatNum(lamda1, 2)}${formatNum(beta1, 6)}${formatNum(alpha2, 2)}${formatNum(theta2, 1)}${formatNum(lamda2, 2)}${formatNum(beta2, 7)}
$#       r        xd         w        d1        d2
${formatNum(R, 5)}${formatNum(XD, 3)}${formatNum(0.065, 3)}${formatNum(0.000611, 6)}${formatNum(0.000002, 6)}
$#       b       gfc         d       gft       gfs      pwrc      pwrt      pmod
${formatNum(B, 1)}${formatNum(GFC, 1)}${formatNum(D, 1)}${formatNum(GFT, 2)}${formatNum(GFS, 2)}${formatNum(PWRC, 1)}${formatNum(PWRT, 1)}${formatNum(PMOD, 1)}
$#   eta0c        nc     etaot        nt     overc     overt     srate     rep0w
${formatNum(eta0c, 7)}${formatNum(nc, 2)}${formatNum(eta0t, 7)}${formatNum(nt, 2)}${formatNum(overc, 5)}${formatNum(overt, 5)}${formatNum(srate, 1)}${formatNum(repow, 1)}
*END`;

            currentContent = content;
            currentFilename = `MAT_CSCM_${fcVal}MPa.k`;

            // Update UI
            document.getElementById('filename').textContent = '📄 ' + currentFilename;
            document.getElementById('param-g').textContent = G.toFixed(1) + ' MPa';
            document.getElementById('param-k').textContent = K.toFixed(1) + ' MPa';
            document.getElementById('param-gf').textContent = GF.toFixed(4);
            document.getElementById('param-alpha').textContent = alpha.toFixed(4);
            document.getElementById('preview').textContent = content;
            document.getElementById('results').classList.remove('hidden');

            // Scroll to results
            document.getElementById('results').scrollIntoView({ behavior: 'smooth' });
        }

        function downloadFile() {
            const blob = new Blob([currentContent], { type: 'text/plain' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = currentFilename;
            a.click();
            URL.revokeObjectURL(url);
        }
    </script>
</body>
</html>
</div>
