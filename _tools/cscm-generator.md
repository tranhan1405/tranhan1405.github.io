---
title: "CSCM Material Generator"
excerpt: "Generate LS-DYNA CSCM material model files for concrete simulations"
collection: tools
permalink: /tools/cscm-generator/
date: 2024-12-12
---

A small helper I use to write `*MAT_CSCM_TITLE` cards for LS-DYNA. Give it the characteristic strength of the concrete and the maximum aggregate size; it computes the elastic, fracture and rate parameters and writes a keyword file you can include in a model. Units are mm, ms, g, MPa (density 0.0023 g/mm³).

<style>
    .cscm { margin: 1.5em 0; }
    .cscm fieldset {
        border: 1px solid #ddd;
        padding: 1em 1.25em 0.75em;
        margin: 0 0 1.5em;
    }
    .cscm legend {
        font-weight: bold;
        padding: 0 0.4em;
        width: auto;
        border: 0;
        margin: 0;
    }
    .cscm label {
        display: block;
        font-weight: bold;
        font-size: 0.9em;
        margin: 0.75em 0 0.25em;
    }
    .cscm-input {
        width: 10em;
        padding: 0.3em 0.5em;
        font-size: 1em;
        border: 1px solid #bbb;
        border-radius: 2px;
    }
    .cscm-input.error { border-color: #b00020; }
    .cscm .hint {
        font-size: 0.8em;
        color: #666;
        margin: 0.25em 0 0;
    }
    .error-message, .warning-message {
        display: none;
        font-size: 0.8em;
        margin: 0.25em 0 0;
    }
    .error-message { color: #b00020; }
    .warning-message { color: #8a6100; }
    .error-message.show, .warning-message.show { display: block; }
    .preset-btn {
        font-size: 0.85em;
        padding: 0.25em 0.6em;
        margin: 0 0.3em 0.4em 0;
        background: #fff;
        border: 1px solid #bbb;
        border-radius: 2px;
        cursor: pointer;
    }
    .preset-btn:hover, .preset-btn.active { border-color: #333; background: #f3f3f3; }
    .cscm .actions { margin-top: 1em; }
    #generate-btn:disabled { opacity: 0.5; cursor: not-allowed; }
    .cscm table.params { font-size: 0.9em; margin: 0.75em 0; }
    .cscm table.params td:last-child { text-align: right; font-family: Monaco, Consolas, "Lucida Console", monospace; }
    .cscm pre#preview {
        font-size: 0.7em;
        line-height: 1.35;
        max-height: 22em;
        overflow: auto;
        white-space: pre;
    }
    #history-list { font-size: 0.85em; margin: 0.5em 0 0; padding-left: 1.2em; }
    .history-item { cursor: pointer; color: #52adc8; margin-bottom: 0.2em; }
    .history-item:hover { text-decoration: underline; }
    .history-time { color: #888; margin-left: 0.5em; }
    #clear-btn {
        font-size: 0.8em;
        background: none;
        border: 0;
        padding: 0;
        color: #888;
        text-decoration: underline;
        cursor: pointer;
    }
</style>

<div class="cscm">
    <fieldset>
        <legend>Input</legend>

        <label>Eurocode 2 grade (optional shortcut)</label>
        <div>
            <button class="preset-btn" data-fc="20" data-dmax="16">C20/25</button>
            <button class="preset-btn" data-fc="25" data-dmax="16">C25/30</button>
            <button class="preset-btn" data-fc="30" data-dmax="16">C30/37</button>
            <button class="preset-btn" data-fc="35" data-dmax="20">C35/45</button>
            <button class="preset-btn" data-fc="40" data-dmax="20">C40/50</button>
            <button class="preset-btn" data-fc="45" data-dmax="20">C45/55</button>
            <button class="preset-btn" data-fc="50" data-dmax="25">C50/60</button>
        </div>

        <label for="fc">f<sub>ck</sub>, characteristic cylinder strength [MPa]</label>
        <input type="number" id="fc" value="30" class="cscm-input" step="0.1" />
        <p class="error-message" id="fc-error">f<sub>ck</sub> must be between 10 and 100 MPa.</p>
        <p class="warning-message" id="fc-warning">Outside 20–80 MPa the fitted parameters are extrapolated.</p>
        <p class="hint">e.g. C30/37 &rarr; 30 MPa. The mean strength is taken as f<sub>cm</sub> = f<sub>ck</sub> + 8 MPa.</p>

        <label for="dmax">d<sub>max</sub>, maximum aggregate size [mm]</label>
        <input type="number" id="dmax" value="16" class="cscm-input" step="1" />
        <p class="error-message" id="dmax-error">d<sub>max</sub> must be between 4 and 40 mm.</p>
        <p class="warning-message" id="dmax-warning">Not a standard size (8, 16, 20, 25, 32 mm).</p>

        <div class="actions">
            <button class="btn btn--primary" id="generate-btn">Generate .k file</button>
        </div>
    </fieldset>

    <div id="results" style="display: none;">
        <h3>Output: <code id="filename"></code></h3>

        <table class="params">
            <tbody>
                <tr><td>Shear modulus G [MPa]</td><td id="param-g"></td></tr>
                <tr><td>Bulk modulus K [MPa]</td><td id="param-k"></td></tr>
                <tr><td>Fracture energy G<sub>F</sub> [N/mm]</td><td id="param-gf"></td></tr>
                <tr><td>&alpha; (TXC surface)</td><td id="param-alpha"></td></tr>
            </tbody>
        </table>

        <pre id="preview"></pre>

        <p><button class="btn" id="download-btn">Download .k file</button></p>
    </div>

    <p style="margin-bottom: 0;"><strong>Recently generated</strong> <button id="clear-btn" style="display: none;">clear</button></p>
    <div id="history-list"><p class="hint">Nothing yet.</p></div>
</div>

### Relations used

Mean strength and elastic constants (Poisson's ratio taken as 0.2):

$$f_{cm} = f_{ck} + 8, \qquad E = 21500\,(f_{cm}/10)^{1/3}, \qquad G = \frac{E}{2(1+\nu)}, \quad K = \frac{E}{3(1-2\nu)}$$

Fracture energy in tension, after CEB-FIP Model Code 1990, with $$G_{FC} = 100\,G_F$$ and $$G_{FS} = G_{FT} = G_F$$:

$$G_{F0} = 0.021 + 5.357\times10^{-4}\,d_{max}, \qquad G_F = G_{F0}\,(f_{cm}/10)^{0.7}$$

The yield surface, cap and rate parameters (α, θ, λ, β, R, X<sub>D</sub>, η<sub>0c</sub>, η<sub>0t</sub>, ...) are computed from curve fits in terms of f<sub>ck</sub>, the same ones used in my Python script. Other values are left at their defaults: ERODE = 1.05, B = 100, D = 0.1, PWRC = 5, PWRT = 1.

The material ID is random (100–999); change it to fit your model. The fits were calibrated for normal-strength concrete, so check results against test data before using them for high-strength mixes.

<script>
    let currentContent = '';
    let currentFilename = '';
    const MAX_HISTORY = 10;
    const HISTORY_KEY = 'cscm-history';

    function formatNum(num, decimals) {
        return num.toFixed(decimals).padStart(10);
    }

    // ---- validation ----
    function validateInputs() {
        const fcEl = document.getElementById('fc');
        const dmaxEl = document.getElementById('dmax');
        const fc = parseFloat(fcEl.value);
        const dmax = parseFloat(dmaxEl.value);

        let isValid = true;

        const fcError = document.getElementById('fc-error');
        const fcWarning = document.getElementById('fc-warning');

        if (isNaN(fc) || fc < 10 || fc > 100) {
            fcEl.classList.add('error');
            fcError.classList.add('show');
            fcWarning.classList.remove('show');
            isValid = false;
        } else {
            fcEl.classList.remove('error');
            fcError.classList.remove('show');
            if (fc < 20 || fc > 80) {
                fcWarning.classList.add('show');
            } else {
                fcWarning.classList.remove('show');
            }
        }

        const dmaxError = document.getElementById('dmax-error');
        const dmaxWarning = document.getElementById('dmax-warning');

        if (isNaN(dmax) || dmax < 4 || dmax > 40) {
            dmaxEl.classList.add('error');
            dmaxError.classList.add('show');
            dmaxWarning.classList.remove('show');
            isValid = false;
        } else {
            dmaxEl.classList.remove('error');
            dmaxError.classList.remove('show');
            const standard = [8, 16, 20, 25, 32];
            if (!standard.includes(dmax)) {
                dmaxWarning.classList.add('show');
            } else {
                dmaxWarning.classList.remove('show');
            }
        }

        const genBtn = document.getElementById('generate-btn');
        if (genBtn) genBtn.disabled = !isValid;
        return isValid;
    }

    // ---- presets ----
    function loadPreset(fc, dmax, btn) {
        document.getElementById('fc').value = fc;
        document.getElementById('dmax').value = dmax;
        validateInputs();

        document.querySelectorAll('.preset-btn').forEach(b => b.classList.remove('active'));
        if (btn) btn.classList.add('active');
    }

    // ---- material parameters (same as the Python script) ----
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
        const nu = 0.2;
        const E = Ec0 * Math.pow(fcm / fcm0, 1/3);
        const G = E / (2 * (1 + nu));
        const K = E / (3 * (1 - 2 * nu));
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
        const eta0c = 1.2772337e-11 * (fpsi ** 2) - 1.0613722e-7 * fpsi + 3.203497e-4;
        const nc = 0.78;
        const eta0t = 8.0614774e-13 * (fc ** 2) - 9.77736719e-10 * fc + 5.0752351e-5;
        const nt = 0.48;
        const overc = 1.309663e-2 * (fc ** 2) - 0.3927659 * fc + 21.45;
        const overt = 1.309663e-2 * (fc ** 2) - 0.3927659 * fc + 21.45;
        const srate = 1.0;
        const repow = 1.0;
        return { eta0c, nc, eta0t, nt, overc, overt, srate, repow };
    }

    function calculateRXD(fc) {
        const R = 4.45994 * Math.exp(-fc / 11.51679) + 1.95358;
        const XD = 17.087 + 1.892 * fc;
        return { R, XD };
    }

    // ---- history (kept in the browser) ----
    function getHistory() {
        try {
            const raw = localStorage.getItem(HISTORY_KEY);
            if (!raw) return [];
            return JSON.parse(raw) || [];
        } catch (e) {
            return [];
        }
    }

    function setHistory(history) {
        try {
            localStorage.setItem(HISTORY_KEY, JSON.stringify(history));
        } catch (e) {}
    }

    function saveToHistory(fc, dmax, filename) {
        const history = getHistory();
        history.unshift({ fc, dmax, filename, timestamp: new Date().toISOString() });
        if (history.length > MAX_HISTORY) {
            history.length = MAX_HISTORY;
        }
        setHistory(history);
        renderHistory();
    }

    function renderHistory() {
        const history = getHistory();
        const list = document.getElementById('history-list');
        const clearBtn = document.getElementById('clear-btn');

        if (!history.length) {
            list.innerHTML = '<p class="hint">Nothing yet.</p>';
            clearBtn.style.display = 'none';
            return;
        }

        clearBtn.style.display = 'inline';
        list.innerHTML = '<ol style="margin: 0; padding: 0;">' + history.map((entry, index) => {
            const date = new Date(entry.timestamp);
            const timeStr = date.toLocaleString('en-GB', {
                day: 'numeric',
                month: 'short',
                hour: '2-digit',
                minute: '2-digit'
            });
            return `<li class="history-item" data-index="${index}">f<sub>ck</sub> = ${entry.fc} MPa, d<sub>max</sub> = ${entry.dmax} mm<span class="history-time">${timeStr}</span></li>`;
        }).join('') + '</ol>';
    }

    function loadFromHistory(index) {
        const entry = getHistory()[index];
        if (!entry) return;
        document.getElementById('fc').value = entry.fc;
        document.getElementById('dmax').value = entry.dmax;
        validateInputs();
        document.getElementById('fc').scrollIntoView({ behavior: 'smooth', block: 'center' });
    }

    function clearHistory() {
        if (!confirm('Clear the list of generated files?')) return;
        try { localStorage.removeItem(HISTORY_KEY); } catch (e) {}
        renderHistory();
    }

    // ---- keyword file ----
    function generateKFile() {
        try {
            if (!validateInputs()) return;

            const fcVal = parseFloat(document.getElementById('fc').value); // fck
            const dmaxVal = parseFloat(document.getElementById('dmax').value);

            const { GF } = calculateFractureEnergy(fcVal, dmaxVal);
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

            const filename = `MAT_CSCM_${fcVal}MPa.k`;
            const lines = [];

            lines.push("$# LS-DYNA Keyword file created by LS-PrePost(R) V4.8.17 - 24Jun2021");
            lines.push(`$# Created on ${dateStr}`);
            lines.push("*KEYWORD");
            lines.push("*TITLE");
            lines.push("$#                                                                         title");
            lines.push("LS-DYNA keyword deck by LS-PrePost");
            lines.push("*MAT_CSCM_TITLE");
            lines.push(`MAT_CSCM_${fcVal}MPa`);
            lines.push("$#     mid        ro     nplot     incre     irate     erode     recov   itretrc");
            lines.push(
                mid.toString().padStart(10) +
                formatNum(ro, 4) +
                (1).toString().padStart(10) +
                formatNum(0.0, 1) +
                (0).toString().padStart(10) +
                formatNum(1.05, 2) +
                formatNum(0.0, 1) +
                (0).toString().padStart(10)
            );
            lines.push("$#    pred");
            lines.push(formatNum(0.0, 1));
            lines.push("$#       g         k     alpha     theta     lamda      beta        nh        ch");
            lines.push(
                formatNum(G, 1) +
                formatNum(K, 1) +
                formatNum(alpha, 4) +
                formatNum(theta, 7) +
                formatNum(lamda, 5) +
                formatNum(beta, 7) +
                formatNum(0.0, 1) +
                formatNum(0.0, 1)
            );
            lines.push("$#  alpha1    theta1    lamda1     beta1    alpha2    theta2    lamda2     beta2");
            lines.push(
                formatNum(alpha1, 2) +
                formatNum(theta1, 1) +
                formatNum(lamda1, 2) +
                formatNum(beta1, 6) +
                formatNum(alpha2, 2) +
                formatNum(theta2, 1) +
                formatNum(lamda2, 2) +
                formatNum(beta2, 7)
            );
            lines.push("$#       r        xd         w        d1        d2");
            lines.push(
                formatNum(R, 5) +
                formatNum(XD, 3) +
                formatNum(0.065, 3) +
                formatNum(0.000611, 6) +
                formatNum(0.000002, 6)
            );
            lines.push("$#       b       gfc         d       gft       gfs      pwrc      pwrt      pmod");
            lines.push(
                formatNum(B, 1) +
                formatNum(GFC, 1) +
                formatNum(D, 1) +
                formatNum(GFT, 2) +
                formatNum(GFS, 2) +
                formatNum(PWRC, 1) +
                formatNum(PWRT, 1) +
                formatNum(PMOD, 1)
            );
            lines.push("$#   eta0c        nc     etaot        nt     overc     overt     srate     rep0w");
            lines.push(
                formatNum(eta0c, 7) +
                formatNum(nc, 2) +
                formatNum(eta0t, 7) +
                formatNum(nt, 2) +
                formatNum(overc, 5) +
                formatNum(overt, 5) +
                formatNum(srate, 1) +
                formatNum(repow, 1)
            );
            lines.push("*END");

            currentContent = lines.join("\n");
            currentFilename = filename;

            document.getElementById('results').style.display = 'block';
            document.getElementById('filename').textContent = filename;
            document.getElementById('preview').textContent = currentContent;
            document.getElementById('param-g').textContent = G.toFixed(1);
            document.getElementById('param-k').textContent = K.toFixed(1);
            document.getElementById('param-gf').textContent = GF.toFixed(4);
            document.getElementById('param-alpha').textContent = alpha.toFixed(4);

            saveToHistory(fcVal, dmaxVal, filename);
        } catch (err) {
            console.error(err);
            alert('Could not generate the file: ' + err.message);
        }
    }

    function downloadFile() {
        if (!currentContent || !currentFilename) return;
        const blob = new Blob([currentContent], { type: 'text/plain;charset=utf-8' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = currentFilename;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
    }

    window.addEventListener('load', function () {
        validateInputs();
        renderHistory();

        document.getElementById('generate-btn').addEventListener('click', generateKFile);
        document.getElementById('download-btn').addEventListener('click', downloadFile);
        document.getElementById('clear-btn').addEventListener('click', clearHistory);

        document.querySelectorAll('.preset-btn').forEach(btn => {
            btn.addEventListener('click', function () {
                loadPreset(parseFloat(this.dataset.fc), parseFloat(this.dataset.dmax), this);
            });
        });

        document.getElementById('fc').addEventListener('input', validateInputs);
        document.getElementById('dmax').addEventListener('input', validateInputs);

        document.getElementById('history-list').addEventListener('click', function (e) {
            const item = e.target.closest('.history-item');
            if (!item) return;
            const idx = parseInt(item.dataset.index, 10);
            if (!isNaN(idx)) loadFromHistory(idx);
        });
    });
</script>
