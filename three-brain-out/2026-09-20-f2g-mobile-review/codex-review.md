Reading additional input from stdin...
OpenAI Codex v0.154.0
--------
workdir: /Users/jc/Desktop/cluade/f2g-longroad
model: gpt-6-astra
provider: openai
approval: never
sandbox: read-only
reasoning effort: medium
reasoning summaries: none
session id: 01a0bd4e-572e-7a20-b8eb-62fa48309d42
--------
user
Review this. Answer the three questions. Be specific and cite the actual CSS/markup. Flag real defects only, no style opinions. Under 700 words.

<stdin>
# CONTEXT
Static single-file marketing site, index.html (no build step). Deployed to Vercel.
Header is position:fixed, z-index 100, measured 83px tall at 375px viewport.
Sections use .section{min-height:min(92vh,900px);display:flex;align-items:center;padding:72px clamp(22px,6vw,90px)}
New section #proof was added this commit with reduced padding: clamp(48px,6.5vh,78px).

# MEASURED DEFECT (reproduced in Chrome at 375x812, section top aligned to viewport top)
header height 83px. Top of each section's .kick-c eyebrow:
  #how   kick top 73px  -> UNDER header
  #proof kick top 54px  -> UNDER header (worst, new section)
  #calc  kick top 74px  -> UNDER header
  #apply kick top 74px  -> UNDER header
  #cta   kick top 73px  -> UNDER header
  #explainer 130px, #industries 145px, #xray 241px -> clear
No horizontal overflow: document.scrollWidth == innerWidth == 375.

# QUESTION
1. Is adding scroll-margin-top / padding-top to clear the fixed header the right fix, or is there a better structural fix?
2. Review the diff below for OTHER mobile defects I have not found: tap targets, contrast, reflow, the <table> that becomes CSS grid under 860px, the SVG using CSS custom properties for fill, accessibility.
3. Flag anything that will break in Safari iOS specifically.
Be specific, cite line content. Do not rewrite the file.

# DIFF (this commit)
diff --git a/index.html b/index.html
index 15ee12a..c0bb688 100644
--- a/index.html
+++ b/index.html
@@ -121,6 +121,53 @@ header.on-light .head-phone{text-shadow:none}
 .section.in .stag > *:nth-child(2){transition-delay:.12s}
 .section.in .stag > *:nth-child(3){transition-delay:.24s}
 
+/* ============ PROOF (Fyrex CI test data) ============
+   Compact band between the delivery run and the explainer film.
+   Heading sits beside the table, not above it, so the section stays
+   short. Below 860px each row folds into label / test / value. */
+#proof{background:var(--ink);color:var(--ivory);min-height:0;padding:clamp(48px,6.5vh,78px) clamp(22px,6vw,90px)}
+#proof .kick-c{color:var(--gold-hot)}
+#proof .wrap-c{min-width:0}   /* flex child must be allowed to shrink under the table */
+#proof .display{font-size:clamp(34px,4.2vw,60px)}
+#proof .lede{color:rgba(244,243,239,.8);max-width:44ch;margin-top:16px;font-size:16px}
+.proof-grid{display:grid;grid-template-columns:minmax(0,.78fr) minmax(0,1.22fr);gap:clamp(26px,4.5vw,72px);align-items:center}
+.proof-intro .lede{margin-top:18px}
+.proof-src{margin-top:22px;padding-top:16px;border-top:1px solid rgba(244,243,239,.16);font-family:'IBM Plex Mono',monospace;font-size:10px;letter-spacing:.22em;text-transform:uppercase;color:rgba(244,243,239,.48)}
+.proof-table{width:100%;border-collapse:collapse}
+.proof-table caption{position:absolute;width:1px;height:1px;overflow:hidden;clip:rect(0 0 0 0);white-space:nowrap}
+.proof-table th{font-family:'IBM Plex Mono',monospace;font-size:10px;letter-spacing:.2em;text-transform:uppercase;color:rgba(244,243,239,.55);font-weight:500;text-align:left;padding:0 0 12px;border-bottom:1px solid rgba(244,243,239,.3)}
+.proof-table th.num{text-align:right;padding-left:18px}
+.proof-table th.test{padding-left:clamp(16px,2.4vw,36px)}
+.proof-table td{padding:11px 0;border-bottom:1px solid rgba(244,243,239,.1);vertical-align:middle}
+.proof-table td.test{padding-left:clamp(16px,2.4vw,36px)}
+.proof-table tbody tr:last-child td{border-bottom:0}
+.proof-what{color:var(--ivory);font-size:15px;position:relative}
+.proof-table td.num{text-align:right;white-space:nowrap;padding-left:18px}
+.proof-val{font-family:'Barlow Condensed',sans-serif;font-weight:800;font-style:italic;font-size:clamp(25px,2.5vw,34px);line-height:1;color:var(--gold-hot);letter-spacing:.01em}
+.proof-test{font-family:'IBM Plex Mono',monospace;font-size:11px;letter-spacing:.05em;color:rgba(244,243,239,.6);line-height:1.45}
+.proof-table tr.is-key td{background:rgba(232,163,61,.07)}
+.proof-table tr.is-key td:first-child{box-shadow:inset 3px 0 0 var(--gold)}
+.proof-table tr.is-key .proof-what{padding-left:14px;font-weight:500}
+.proof-table tr.is-key .proof-val{color:var(--gold)}
+.proof-table tr.is-key td:last-child{padding-right:14px}
+.proof-foot{margin-top:20px;font-size:13.5px;line-height:1.6;color:rgba(244,243,239,.6);max-width:60ch}
+.proof-foot a{color:var(--gold-hot);text-decoration:none;border-bottom:1px solid rgba(245,193,94,.45)}
+.proof-foot a:hover{border-bottom-color:var(--gold-hot)}
+@media (max-width:860px){
+  .proof-grid{grid-template-columns:minmax(0,1fr);gap:26px}
+  .proof-table,.proof-table tbody{display:block;width:100%}
+  .proof-table thead{display:none}
+  .proof-table tbody tr{display:grid;grid-template-columns:minmax(0,1fr) auto;gap:2px clamp(14px,4vw,24px);padding:13px 0;border-bottom:1px solid rgba(244,243,239,.1)}
+  .proof-table tbody tr:last-child{border-bottom:0}
+  .proof-table td{border:0;padding:0}
+  .proof-table td.what{grid-column:1;grid-row:1}
+  .proof-table td.test{grid-column:1;grid-row:2;padding-left:0}
+  .proof-table td.num{grid-column:2;grid-row:1/span 2;align-self:center;padding-left:0}
+  .proof-table tr.is-key{background:rgba(232,163,61,.07);box-shadow:inset 3px 0 0 var(--gold)}
+  .proof-table tr.is-key td{background:none;box-shadow:none}
+  .proof-table tr.is-key td:last-child{padding-right:12px}
+}
+
 /* ============ FILM (Fyrex explainer) ============ */
 #explainer{background:var(--ink);color:var(--ivory)}
 #explainer .kick-c{color:var(--gold-hot)}
@@ -293,6 +340,26 @@ header.on-light .head-phone{text-shadow:none}
    pasting this in. Everything below is namespaced run-*.
    =================================================================== */
 
+#how{
+  /* delivery run, brushed steel. Swap these to retheme the whole zone. */
+  --k-fill:linear-gradient(90deg,#7C8085 0%,#A7A9AC 55%,#8F9498 100%);
+  --k-glow:0 0 14px rgba(167,169,172,.22);
+  --k-dash:#D3D6D9;
+  --k-node:#B9BCC0;
+  --k-ring:0 0 0 5px rgba(167,169,172,.24);
+  --k-num:#A7A9AC;
+  --k-line:rgba(167,169,172,.32);
+  --k-day:#C9CCD0;
+  --k-tick:#C9CCD0;
+  --k-stub:rgba(167,169,172,.72);
+  --k-dust:rgba(167,169,172,.26);
+  --k-complete:#C9CCD0;
+  /* the rig: white cab, polished aluminium barrel */
+  --t-barrel:#C4CACF; --t-barrel-hi:#FFFFFF; --t-band:#878E96;
+  --t-cab:#F2F5F8; --t-cab-shade:#D7DDE3; --t-glass:#93A7BC;
+  --t-chassis:#2B3654; --t-tyre:#1C2439; --t-rim:#C4CACF;
+  --t-lamp:#FFF4DC; --t-outline:rgba(3,13,48,.55);
+}
 #how .run-track{position:relative;margin-top:-6px}
 
 /* ---------- the road ---------- */
@@ -318,8 +385,8 @@ header.on-light .head-phone{text-shadow:none}
   width:100%;height:10px;border-radius:3px;
   transform-origin:left center;
   transform:translateY(-50%) scaleX(0);
-  background:linear-gradient(90deg,var(--gold),var(--gold-hot));
-  box-shadow:0 0 18px rgba(232,163,61,.35);
+  background:var(--k-fill);
+  box-shadow:var(--k-glow);
   z-index:2;
   will-change:transform;
 }
@@ -327,7 +394,7 @@ header.on-light .head-phone{text-shadow:none}
   position:absolute;left:0;right:0;top:50%;
   height:2px;transform:translateY(-50%);
   background:repeating-linear-gradient(90deg,
-    var(--gold-hot) 0 16px, rgba(0,0,0,0) 16px 34px);
+    var(--k-dash) 0 16px, rgba(0,0,0,0) 16px 34px);
   opacity:.5;z-index:3;
   -webkit-mask-image:linear-gradient(90deg,rgba(0,0,0,0),#000 7%,#000 93%,rgba(0,0,0,0));
   mask-image:linear-gradient(90deg,rgba(0,0,0,0),#000 7%,#000 93%,rgba(0,0,0,0));
@@ -344,9 +411,9 @@ header.on-light .head-phone{text-shadow:none}
              transform .4s var(--ease-out);
 }
 #how .run-node.is-done{
-  background:var(--gold-hot);
+  background:var(--k-node);
   border-color:var(--ink);
-  box-shadow:0 0 0 5px rgba(232,163,61,.22);
+  box-shadow:var(--k-ring);
   transform:translate(-50%,-50%) scale(1.14);
 }
 
@@ -372,7 +439,7 @@ header.on-light .head-phone{text-shadow:none}
   background:linear-gradient(90deg,
     rgba(3,13,48,0) 0%,
     rgba(3,13,48,.10) 45%,
-    rgba(232,163,61,.30) 100%);
+    var(--k-dust) 100%);
   opacity:var(--run-dust,0);
   transition:opacity .12s linear;
   pointer-events:none;
@@ -380,7 +447,7 @@ header.on-light .head-phone{text-shadow:none}
 #how .run-dust::after{
   content:'';position:absolute;left:6px;right:18px;top:50%;height:1px;
   transform:translateY(-50%);
-  background:linear-gradient(90deg,rgba(0,0,0,0),var(--gold));
+  background:linear-gradient(90deg,rgba(0,0,0,0),var(--k-dash));
   opacity:.55;
 }
 
@@ -423,11 +490,11 @@ header.on-light .head-phone{text-shadow:none}
 #how .run-card.is-done{
   --c-bg:var(--ink);
   --c-fg:rgba(244,243,239,.80);
-  --c-num:var(--gold);
-  --c-line:rgba(232,163,61,.30);
-  --c-day:var(--gold-hot);
-  --c-tick:var(--gold-hot);
-  --c-stub:rgba(232,163,61,.75);
+  --c-num:var(--k-num);
+  --c-line:var(--k-line);
+  --c-day:var(--k-day);
+  --c-tick:var(--k-tick);
+  --c-stub:var(--k-stub);
   transform:translateY(-5px);
   box-shadow:0 22px 44px -24px rgba(3,13,48,.60);
 }
@@ -477,7 +544,7 @@ header.on-light .head-phone{text-shadow:none}
   transition:opacity .35s var(--ease-out);
 }
 #how .run-st-a{color:rgba(3,13,48,.40);opacity:1}
-#how .run-st-b{color:var(--gold-hot);opacity:0}
+#how .run-st-b{color:var(--k-complete);opacity:0}
 #how .run-card.is-done .run-st-a{opacity:0}
 #how .run-card.is-done .run-st-b{opacity:1}
 
@@ -515,7 +582,7 @@ header.on-light .head-phone{text-shadow:none}
     width:2px;height:auto;
     transform:translateX(-50%);
     background:repeating-linear-gradient(180deg,
-      var(--gold-hot) 0 14px, rgba(0,0,0,0) 14px 30px);
+      var(--k-dash) 0 14px, rgba(0,0,0,0) 14px 30px);
     -webkit-mask-image:linear-gradient(180deg,rgba(0,0,0,0),#000 5%,#000 95%,rgba(0,0,0,0));
     mask-image:linear-gradient(180deg,rgba(0,0,0,0),#000 5%,#000 95%,rgba(0,0,0,0));
   }
@@ -911,45 +978,58 @@ footer{background:var(--film-deep);color:var(--ivory);padding:70px clamp(22px,6v
         <div class="run-truck">
           <div class="run-dust"></div>
           <svg viewBox="0 0 132 50" xmlns="http://www.w3.org/2000/svg" focusable="false">
-            <ellipse cx="68" cy="46.4" rx="60" ry="3.2" fill="#030D30" opacity=".16"/>
+            <defs>
+              <linearGradient id="f2gBarrel" x1="0" y1="0" x2="0" y2="1">
+                <stop offset="0" stop-color="var(--t-barrel-hi)"/>
+                <stop offset=".26" stop-color="var(--t-barrel)"/>
+                <stop offset=".54" stop-color="var(--t-barrel-hi)"/>
+                <stop offset="1" stop-color="var(--t-band)"/>
+              </linearGradient>
+              <linearGradient id="f2gCab" x1="0" y1="0" x2="0" y2="1">
+                <stop offset="0" stop-color="var(--t-cab)"/>
+                <stop offset="1" stop-color="var(--t-cab-shade)"/>
+              </linearGradient>
+            </defs>
+
+            <ellipse cx="68" cy="46.4" rx="60" ry="3.2" fill="#030D30" opacity=".18"/>
 
             <!-- trailer -->
-            <rect x="10" y="29.6" width="3.4" height="8.6" rx="1.2" fill="#050818"/>
-            <rect x="12" y="34.8" width="84" height="3.6" fill="#050818"/>
-            <rect x="48" y="8.6" width="12" height="5.4" rx="2" fill="#050818"/>
-            <rect x="12" y="13" width="82" height="22" rx="11" fill="#050818"/>
-            <rect x="17" y="16.4" width="72" height="3.6" rx="1.8" fill="#E8A33D" opacity=".20"/>
-            <path d="M38 13.8V34.2" stroke="#E8A33D" stroke-opacity=".32" stroke-width="1.2"/>
-            <path d="M62 13.8V34.2" stroke="#E8A33D" stroke-opacity=".32" stroke-width="1.2"/>
-            <rect x="12" y="13" width="82" height="22" rx="11" fill="none" stroke="#E8A33D" stroke-opacity=".38"/>
+            <rect x="10" y="29.6" width="3.4" height="8.6" rx="1.2" fill="var(--t-chassis)"/>
+            <rect x="12" y="34.8" width="84" height="3.6" fill="var(--t-chassis)"/>
+            <rect x="48" y="8.6" width="12" height="5.4" rx="2" fill="var(--t-band)"/>
+            <rect x="12" y="13" width="82" height="22" rx="11" fill="url(#f2gBarrel)"/>
+            <rect x="17" y="16.4" width="72" height="3.2" rx="1.6" fill="var(--t-barrel-hi)" opacity=".85"/>
+            <path d="M38 13.8V34.2" stroke="var(--t-band)" stroke-opacity=".55" stroke-width="1.2"/>
+            <path d="M62 13.8V34.2" stroke="var(--t-band)" stroke-opacity=".55" stroke-width="1.2"/>
+            <rect x="12" y="13" width="82" height="22" rx="11" fill="none" stroke="var(--t-outline)" stroke-opacity=".38"/>
 
             <!-- coupling -->
-            <rect x="93" y="31.4" width="7" height="5.6" fill="#050818"/>
-            <rect x="94.4" y="6" width="2.8" height="21" rx="1.4" fill="#050818"/>
+            <rect x="93" y="31.4" width="7" height="5.6" fill="var(--t-chassis)"/>
+            <rect x="94.4" y="6" width="2.8" height="21" rx="1.4" fill="var(--t-band)"/>
 
             <!-- prime mover -->
-            <path d="M96 38V12q0-2.5 2.5-2.5H117q2.5 0 3.6 2.1l4.4 8.8 2.5 1q2.5 1.2 2.5 3.8V36q0 2-2 2Z" fill="#050818"/>
-            <rect x="102.6" y="12.6" width="7.6" height="7.6" rx="1.2" fill="#E8A33D" opacity=".28"/>
-            <path d="M112.6 12.6h5.1q1.1 0 1.6 1l3.2 6.6h-9.9Z" fill="#E8A33D" opacity=".55"/>
-            <rect x="125.6" y="28.2" width="5" height="6.2" rx="1.2" fill="#E8A33D" opacity=".32"/>
-            <circle cx="128.2" cy="25" r="1.7" fill="#F5C15E"/>
+            <path d="M96 38V12q0-2.5 2.5-2.5H117q2.5 0 3.6 2.1l4.4 8.8 2.5 1q2.5 1.2 2.5 3.8V36q0 2-2 2Z" fill="url(#f2gCab)" stroke="var(--t-outline)" stroke-opacity=".45" stroke-width=".8"/>
+            <rect x="102.6" y="12.6" width="7.6" height="7.6" rx="1.2" fill="var(--t-glass)"/>
+            <path d="M112.6 12.6h5.1q1.1 0 1.6 1l3.2 6.6h-9.9Z" fill="var(--t-glass)" opacity=".85"/>
+            <rect x="125.6" y="28.2" width="5" height="6.2" rx="1.2" fill="var(--t-chassis)"/>
+            <circle cx="128.2" cy="25" r="1.7" fill="var(--t-lamp)"/>
 
             <!-- wheels -->
-            <circle cx="24" cy="38" r="6.4" fill="#050818" stroke="#E8A33D" stroke-opacity=".35"/>
-            <circle cx="39" cy="38" r="6.4" fill="#050818" stroke="#E8A33D" stroke-opacity=".35"/>
-            <circle cx="102" cy="38" r="6.4" fill="#050818" stroke="#E8A33D" stroke-opacity=".35"/>
-            <circle cx="121" cy="38" r="6.4" fill="#050818" stroke="#E8A33D" stroke-opacity=".35"/>
+            <circle cx="24" cy="38" r="6.4" fill="var(--t-tyre)" stroke="var(--t-outline)" stroke-opacity=".35"/>
+            <circle cx="39" cy="38" r="6.4" fill="var(--t-tyre)" stroke="var(--t-outline)" stroke-opacity=".35"/>
+            <circle cx="102" cy="38" r="6.4" fill="var(--t-tyre)" stroke="var(--t-outline)" stroke-opacity=".35"/>
+            <circle cx="121" cy="38" r="6.4" fill="var(--t-tyre)" stroke="var(--t-outline)" stroke-opacity=".35"/>
 
-            <g class="run-wheel" stroke="#E8A33D" stroke-opacity=".6" stroke-width="1" fill="none">
+            <g class="run-wheel" stroke="var(--t-rim)" stroke-opacity=".85" stroke-width="1" fill="none">
               <circle cx="24" cy="38" r="3"/><path d="M21 38h6M24 35v6"/>
             </g>
-            <g class="run-wheel" stroke="#E8A33D" stroke-opacity=".6" stroke-width="1" fill="none">
+            <g class="run-wheel" stroke="var(--t-rim)" stroke-opacity=".85" stroke-width="1" fill="none">
               <circle cx="39" cy="38" r="3"/><path d="M36 38h6M39 35v6"/>
             </g>
-            <g class="run-wheel" stroke="#E8A33D" stroke-opacity=".6" stroke-width="1" fill="none">
+            <g class="run-wheel" stroke="var(--t-rim)" stroke-opacity=".85" stroke-width="1" fill="none">
               <circle cx="102" cy="38" r="3"/><path d="M99 38h6M102 35v6"/>
             </g>
-            <g class="run-wheel" stroke="#E8A33D" stroke-opacity=".6" stroke-width="1" fill="none">
+            <g class="run-wheel" stroke="var(--t-rim)" stroke-opacity=".85" stroke-width="1" fill="none">
               <circle cx="121" cy="38" r="3"/><path d="M118 38h6M121 35v6"/>
             </g>
           </svg>
@@ -1031,6 +1111,71 @@ footer{background:var(--film-deep);color:var(--ivory);padding:70px clamp(22px,6v
   </div>
 </section>
 
+  <!-- ================== PROOF: Fyrex CI test data ================== -->
+  <section class="section" id="proof">
+    <div class="wrap-c">
+      <div class="proof-grid">
+        <div class="proof-intro">
+          <div class="kick-c">Fyrex CI test data</div>
+          <h2 class="display">Measured,<br>not claimed.</h2>
+          <p class="lede">Tested results for Fyrex CI across emissions, fuel economy, engine noise and engine protection.</p>
+          <p class="proof-src">Source: Fyrex CI test data</p>
+        </div>
+
+        <div>
+          <table class="proof-table">
+            <caption>Fyrex CI tested results by improvement and test type</caption>
+            <thead>
+              <tr>
+                <th scope="col">Tested for</th>
+                <th scope="col" class="num">Improvement</th>
+                <th scope="col" class="test">Type of test</th>
+              </tr>
+            </thead>
+            <tbody>
+              <tr>
+                <td class="what proof-what">CO<sub>2</sub> reduction</td>
+                <td class="num"><span class="proof-val">27%</span></td>
+                <td class="test proof-test">Department of Primary Industries</td>
+              </tr>
+              <tr>
+                <td class="what proof-what">Smoke reduction</td>
+                <td class="num"><span class="proof-val">8%</span></td>
+                <td class="test proof-test">Department of Primary Industries</td>
+              </tr>
+              <tr>
+                <td class="what proof-what">Engine noise</td>
+                <td class="num"><span class="proof-val">15%</span></td>
+                <td class="test proof-test">CAT 3520</td>
+              </tr>
+              <tr class="is-key">
+                <td class="what proof-what">Direct improvement in fuel economy</td>
+                <td class="num"><span class="proof-val">7%</span></td>
+                <td class="test proof-test">MTU 4000 series</td>
+              </tr>
+              <tr>
+                <td class="what proof-what">Injector clean up</td>
+                <td class="num"><span class="proof-val">20%</span></td>
+                <td class="test proof-test">Cummins L10 test</td>
+              </tr>
+              <tr>
+                <td class="what proof-what">Injector and pump wear</td>
+                <td class="num"><span class="proof-val">35%</span></td>
+                <td class="test proof-test">ASTM5001 BOCLE</td>
+              </tr>
+              <tr>
+                <td class="what proof-what">Corrosion and rust</td>
+                <td class="num"><span class="proof-val">100%</span></td>
+                <td class="test proof-test">NACE rust test</td>
+              </tr>
+            </tbody>
+          </table>
+          <p class="proof-foot">The 7 percent used in the <a href="#calc">savings calculator</a> is the tested direct improvement in fuel economy result, MTU 4000 series.</p>
+        </div>
+      </div>
+    </div>
+  </section>
+
   <!-- ================== FILM: Fyrex explainer ================== -->
   <section class="section" id="explainer">
     <div class="wrap-c">
</stdin>
codex
I’ll check the surrounding CSS and markup, and verify Safari behavior before flagging compatibility issues.
exec
/bin/zsh -lc "pwd; rg -n 'proof|--ink:|--ivory:|run-truck|scroll|<base|aria-hidden|AGENTS' index.html" in /Users/jc/Desktop/cluade/f2g-longroad
 succeeded in 0ms:
/Users/jc/Desktop/cluade/f2g-longroad
32:  --ink:#030D30;
39:  --ivory:#F4F3EF;
44:html{scroll-behavior:auto}
77:#scrollcue{position:fixed;left:50%;transform:translateX(-50%);bottom:24px;z-index:90;text-align:center;font-family:'IBM Plex Mono',monospace;font-size:11px;letter-spacing:.34em;text-transform:uppercase;color:#fff;opacity:0;transition:opacity .6s ease;text-shadow:0 1px 10px rgba(0,0,0,.75)}
78:#scrollcue.show{opacity:1}
79:#scrollcue::after{content:"";display:block;width:12px;height:12px;border-right:2.5px solid #fff;border-bottom:2.5px solid #fff;transform:rotate(45deg);margin:8px auto 0;filter:drop-shadow(0 1px 6px rgba(0,0,0,.8));animation:cue 1.6s ease-in-out infinite}
83:#film-scroll{position:relative;height:965vh}
128:#proof{background:var(--ink);color:var(--ivory);min-height:0;padding:clamp(48px,6.5vh,78px) clamp(22px,6vw,90px)}
129:#proof .kick-c{color:var(--gold-hot)}
130:#proof .wrap-c{min-width:0}   /* flex child must be allowed to shrink under the table */
131:#proof .display{font-size:clamp(34px,4.2vw,60px)}
132:#proof .lede{color:rgba(244,243,239,.8);max-width:44ch;margin-top:16px;font-size:16px}
133:.proof-grid{display:grid;grid-template-columns:minmax(0,.78fr) minmax(0,1.22fr);gap:clamp(26px,4.5vw,72px);align-items:center}
134:.proof-intro .lede{margin-top:18px}
135:.proof-src{margin-top:22px;padding-top:16px;border-top:1px solid rgba(244,243,239,.16);font-family:'IBM Plex Mono',monospace;font-size:10px;letter-spacing:.22em;text-transform:uppercase;color:rgba(244,243,239,.48)}
136:.proof-table{width:100%;border-collapse:collapse}
137:.proof-table caption{position:absolute;width:1px;height:1px;overflow:hidden;clip:rect(0 0 0 0);white-space:nowrap}
138:.proof-table th{font-family:'IBM Plex Mono',monospace;font-size:10px;letter-spacing:.2em;text-transform:uppercase;color:rgba(244,243,239,.55);font-weight:500;text-align:left;padding:0 0 12px;border-bottom:1px solid rgba(244,243,239,.3)}
139:.proof-table th.num{text-align:right;padding-left:18px}
140:.proof-table th.test{padding-left:clamp(16px,2.4vw,36px)}
141:.proof-table td{padding:11px 0;border-bottom:1px solid rgba(244,243,239,.1);vertical-align:middle}
142:.proof-table td.test{padding-left:clamp(16px,2.4vw,36px)}
143:.proof-table tbody tr:last-child td{border-bottom:0}
144:.proof-what{color:var(--ivory);font-size:15px;position:relative}
145:.proof-table td.num{text-align:right;white-space:nowrap;padding-left:18px}
146:.proof-val{font-family:'Barlow Condensed',sans-serif;font-weight:800;font-style:italic;font-size:clamp(25px,2.5vw,34px);line-height:1;color:var(--gold-hot);letter-spacing:.01em}
147:.proof-test{font-family:'IBM Plex Mono',monospace;font-size:11px;letter-spacing:.05em;color:rgba(244,243,239,.6);line-height:1.45}
148:.proof-table tr.is-key td{background:rgba(232,163,61,.07)}
149:.proof-table tr.is-key td:first-child{box-shadow:inset 3px 0 0 var(--gold)}
150:.proof-table tr.is-key .proof-what{padding-left:14px;font-weight:500}
151:.proof-table tr.is-key .proof-val{color:var(--gold)}
152:.proof-table tr.is-key td:last-child{padding-right:14px}
153:.proof-foot{margin-top:20px;font-size:13.5px;line-height:1.6;color:rgba(244,243,239,.6);max-width:60ch}
154:.proof-foot a{color:var(--gold-hot);text-decoration:none;border-bottom:1px solid rgba(245,193,94,.45)}
155:.proof-foot a:hover{border-bottom-color:var(--gold-hot)}
157:  .proof-grid{grid-template-columns:minmax(0,1fr);gap:26px}
158:  .proof-table,.proof-table tbody{display:block;width:100%}
159:  .proof-table thead{display:none}
160:  .proof-table tbody tr{display:grid;grid-template-columns:minmax(0,1fr) auto;gap:2px clamp(14px,4vw,24px);padding:13px 0;border-bottom:1px solid rgba(244,243,239,.1)}
161:  .proof-table tbody tr:last-child{border-bottom:0}
162:  .proof-table td{border:0;padding:0}
163:  .proof-table td.what{grid-column:1;grid-row:1}
164:  .proof-table td.test{grid-column:1;grid-row:2;padding-left:0}
165:  .proof-table td.num{grid-column:2;grid-row:1/span 2;align-self:center;padding-left:0}
166:  .proof-table tr.is-key{background:rgba(232,163,61,.07);box-shadow:inset 3px 0 0 var(--gold)}
167:  .proof-table tr.is-key td{background:none;box-shadow:none}
168:  .proof-table tr.is-key td:last-child{padding-right:12px}
183:/* ============ SERVICE PILLARS (horizontal scroll) ============
184:   Popberry-style pinned sideways scroller in f2g colours.
186:   track slides left as you scroll through. Wavy seams between
209:.pillar--ivory::before{background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='26' height='90' viewBox='0 0 26 90'%3E%3Cpath d='M26 0 L10 0 Q2 11.25 10 22.5 T10 45 T10 67.5 T10 90 L26 90 Z' fill='%23F4F3EF'/%3E%3C/svg%3E")}
210:.pillar--ink::before{display:none}
311:   compress and dim. Mobile: native scroll-snap swipe carousel.
326:  .ind-acc{height:auto;overflow-x:auto;scroll-snap-type:x mandatory;-webkit-overflow-scrolling:touch;scrollbar-width:none}
327:  .ind-acc::-webkit-scrollbar{display:none}
328:  .ind-panel{flex:0 0 82%;aspect-ratio:3/4;scroll-snap-align:center;transition:none}
421:#how .run-truck{
429:#how .run-truck svg{display:block;width:100%;height:100%;overflow:visible}
589:  #how .run-truck{
610:  #how .run-truck{width:88px;height:33px;transform:translate3d(-44px,-140px,0) rotate(90deg)}
621:  #how .run-truck{display:none}
842:  #film-scroll{height:857vh}
856:  #scrollcue::after{animation:none}
882:<div id="scrollcue">Scroll</div>
884:<div id="film-scroll">
928:      <img class="xray-layer xray-breathe" src="assets/xray-glow.png" alt="" aria-hidden="true" loading="lazy">
929:      <img class="xray-layer xray-fire" src="assets/xray-eng.png" alt="" aria-hidden="true" loading="lazy">
968:      <div class="run-road" aria-hidden="true">
978:        <div class="run-truck">
1048:            <svg class="run-tick" viewBox="0 0 20 20" aria-hidden="true" focusable="false"><path d="M4 10.6L8.4 15L16 5.6"/></svg>
1064:            <svg class="run-tick" viewBox="0 0 20 20" aria-hidden="true" focusable="false"><path d="M4 10.6L8.4 15L16 5.6"/></svg>
1080:            <svg class="run-tick" viewBox="0 0 20 20" aria-hidden="true" focusable="false"><path d="M4 10.6L8.4 15L16 5.6"/></svg>
1096:            <svg class="run-tick" viewBox="0 0 20 20" aria-hidden="true" focusable="false"><path d="M4 10.6L8.4 15L16 5.6"/></svg>
1115:  <section class="section" id="proof">
1117:      <div class="proof-grid">
1118:        <div class="proof-intro">
1122:          <p class="proof-src">Source: Fyrex CI test data</p>
1126:          <table class="proof-table">
1137:                <td class="what proof-what">CO<sub>2</sub> reduction</td>
1138:                <td class="num"><span class="proof-val">27%</span></td>
1139:                <td class="test proof-test">Department of Primary Industries</td>
1142:                <td class="what proof-what">Smoke reduction</td>
1143:                <td class="num"><span class="proof-val">8%</span></td>
1144:                <td class="test proof-test">Department of Primary Industries</td>
1147:                <td class="what proof-what">Engine noise</td>
1148:                <td class="num"><span class="proof-val">15%</span></td>
1149:                <td class="test proof-test">CAT 3520</td>
1152:                <td class="what proof-what">Direct improvement in fuel economy</td>
1153:                <td class="num"><span class="proof-val">7%</span></td>
1154:                <td class="test proof-test">MTU 4000 series</td>
1157:                <td class="what proof-what">Injector clean up</td>
1158:                <td class="num"><span class="proof-val">20%</span></td>
1159:                <td class="test proof-test">Cummins L10 test</td>
1162:                <td class="what proof-what">Injector and pump wear</td>
1163:                <td class="num"><span class="proof-val">35%</span></td>
1164:                <td class="test proof-test">ASTM5001 BOCLE</td>
1167:                <td class="what proof-what">Corrosion and rust</td>
1168:                <td class="num"><span class="proof-val">100%</span></td>
1169:                <td class="test proof-test">NACE rust test</td>
1173:          <p class="proof-foot">The 7 percent used in the <a href="#calc">savings calculator</a> is the tested direct improvement in fuel economy result, MTU 4000 series.</p>
1314:    <div class="marq" aria-hidden="true"><div class="track"><span>More kilometres per tank <em>&nbsp;·&nbsp;</em> Cleaner burn <em>&nbsp;·&nbsp;</em> Additive in every litre <em>&nbsp;·&nbsp;</em> Australia + New Zealand <em>&nbsp;·&nbsp;</em> </span><span>More kilometres per tank <em>&nbsp;·&nbsp;</em> Cleaner burn <em>&nbsp;·&nbsp;</em> Additive in every litre <em>&nbsp;·&nbsp;</em> Australia + New Zealand <em>&nbsp;·&nbsp;</em> </span></div></div>
1347:<aside id="drawer" aria-hidden="true" aria-label="Enquiry form">
1354:    <input class="hp" type="text" name="company_website" tabindex="-1" autocomplete="off" aria-hidden="true">
1417:if (JUMP !== null) history.scrollRestoration = 'manual';
1420:var stage = document.getElementById('stage'), filmScroll = document.getElementById('film-scroll');
1506:     decodes at scroll speed, which is what OOM-killed the tab on phones */
1568:/* ---------- scroll / progress ---------- */
1591:var readout = document.getElementById('readout'), cue = document.getElementById('scrollcue');
1798:    drawer.setAttribute('aria-hidden','false');
1805:    drawer.setAttribute('aria-hidden','true');
1860:/* THE DELIVERY RUN — scroll-driven tanker on the #how road */
1867:  var truck = sec.querySelector('.run-truck');
2031:  window.addEventListener('scroll', schedule, { passive: true });
2058:      scrollTo(0, +JUMP || 0);

exec
/bin/zsh -lc 'cat /Users/jc/.agents/skills/agent-reach/SKILL.md' in /Users/jc/Desktop/cluade/f2g-longroad
 succeeded in 0ms:
---
name: agent-reach
description: >
  MUST USE when user wants to research/search/look up/find anything on the
  internet — e.g. "research this topic", "do a deep dive on X", "search the
  web for X", "see what people say about X", "look this up".

  Also MUST USE when user mentions any platform or shares any URL/link:
  Twitter/X, Reddit, YouTube, GitHub, Bilibili, XiaoHongShu,
  Xiaoyuzhou Podcast, LinkedIn/jobs/recruiting, V2EX, Xueqiu (stocks), RSS.

  13 platforms, multi-backend routing (OpenCLI / per-platform CLIs / APIs).
  Zero config for 6 channels. Run `agent-reach doctor --json` to see which
  backend serves each platform right now.

  NOT for: writing reports/analysis/translation (this skill only FETCHES
  internet content); posting/commenting/liking (write operations); platforms
  that already have a dedicated skill installed (prefer that skill).
metadata:
  openclaw:
    homepage: https://github.com/Panniantong/Agent-Reach
---

# Agent Reach — internet capability router

13 platforms, multiple backends each. **When this skill exists, use it for
these platforms — do not invent your own approach.**

## Standing rules (apply for the whole session)

1. **Health-check before acting**: for multi-backend platforms (XiaoHongShu /
   Reddit / Bilibili / Twitter), run `agent-reach doctor --json` first and
   pick the command group matching each platform's `active_backend`.
2. **Announce what you use**: say "using agent-reach, platform X via backend Y"
   before starting.
3. **On failure, follow the retry chains in references/** — never guess
   commands.
4. **For broad research tasks**: combine platforms (Exa for web search +
   Twitter/Reddit for discussions + XiaoHongShu/Bilibili for Chinese
   perspectives), collect in parallel, then synthesize.
5. **Watch versions for the user**: after finishing a substantial
   multi-platform task, run `agent-reach check-update` (fast, one API call).
   If a new version exists, append one line to your wrap-up: "Agent Reach
   vX.Y.Z is available — paste this to me to update: 帮我更新 Agent Reach：
   https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/update.md".
   Never interrupt the current task to update; never nag about the same version twice.

## Routing table

| User intent | Category | Details |
|---------|------|---------|
| Web / code search | search | [references/search.md](references/search.md) |
| XiaoHongShu / Twitter / Bilibili / V2EX / Reddit | social | [references/social.md](references/social.md) |
| Jobs / LinkedIn | career | [references/career.md](references/career.md) |
| GitHub / code | dev | [references/dev.md](references/dev.md) |
| Web pages / articles / RSS | web | [references/web.md](references/web.md) |
| YouTube / Bilibili / podcast transcripts | video | [references/video.md](references/video.md) |

## Zero-config quick commands

```bash
# Exa web search
mcporter call 'exa.web_search_exa(query: "query", numResults: 5)'

# Read any web page
curl -s "https://r.jina.ai/URL"

# GitHub search
gh search repos "query" --sort stars --limit 10

# YouTube subtitles (NOTE: never use yt-dlp for Bilibili — see video.md)
yt-dlp --write-sub --skip-download -o "/tmp/%(id)s" "URL"

# V2EX hot topics
curl -s "https://www.v2ex.com/api/topics/hot.json" -H "User-Agent: agent-reach/1.0"

# Bilibili search (bili-cli, no login needed)
bili search "query" --type video -n 5
```

## Login-backed platforms (pick by doctor's active_backend)

```bash
# Twitter search (twitter-cli preferred; retry chain in social.md)
twitter search "query" -n 10

# Reddit (NO zero-config path — OpenCLI or rdt-cli, login required)
opencli reddit search "query" -f yaml   # desktop
rdt search "query" --limit 10            # legacy/server

# XiaoHongShu (desktop prefers OpenCLI)
opencli xiaohongshu search "query" -f yaml
```

## Environment check

```bash
# Channel availability + which backend serves each platform
agent-reach doctor --json
```

## Workspace rules

**Never create files in the agent workspace.** Use `/tmp/` for temporary
output and `~/.agent-reach/` for persistent data.

## Detailed references

Read the matching file when you need specifics (commands above cover the
common cases; references hold per-backend command groups, caveats, retry
chains — note: reference docs are written in Chinese, commands are universal):

- [Search](references/search.md) — Exa AI search
- [Social](references/social.md) — XiaoHongShu, Twitter, Bilibili, V2EX, Reddit (multi-backend groups)
- [Career](references/career.md) — LinkedIn
- [Dev](references/dev.md) — GitHub CLI
- [Web](references/web.md) — Jina Reader, RSS
- [Video](references/video.md) — YouTube, Bilibili, Xiaoyuzhou

## Configure a channel

If a channel needs setup, fetch the install guide:
https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/install.md

The user only provides cookies / one extension click; the agent does the rest.

codex
The local file matches the diff, and the truck already sits inside `aria-hidden="true"`. I’m using agent-reach for the compatibility lookup, with primary browser documentation to distinguish confirmed defects from possible risks.
exec
/bin/zsh -lc "sed -n '30,72p' index.html; sed -n '102,121p' index.html; cat /Users/jc/.agents/skills/agent-reach/references/search.md" in /Users/jc/Desktop/cluade/f2g-longroad
 succeeded in 0ms:
:root{
  /* navy + grey ramp sampled directly from the official logo artwork */
  --ink:#030D30;
  --film-deep:#050818;
  --silver:#A7A9AC;        /* logo keyline */
  --steel:#6D6E71;         /* logo chevron, darkest grey */
  --steel-mid:#8A8C90;     /* mid step of the chevron ramp */
  --gold:#E8A33D;
  --gold-hot:#F5C15E;
  --ivory:#F4F3EF;
  --white:#FFFFFF;
  --ease-out:cubic-bezier(.16,1,.3,1);
}
*{margin:0;padding:0;box-sizing:border-box}
html{scroll-behavior:auto}
body{background:var(--film-deep);color:var(--ivory);font-family:'Archivo',sans-serif;font-size:16px;line-height:1.65;-webkit-font-smoothing:antialiased;overflow-x:hidden}
img{max-width:100%;display:block}
a{color:inherit;text-decoration:none}
.mono{font-family:'IBM Plex Mono',monospace}

/* ============ LOADER (non-blocking chip — the poster shows behind it) ============ */
#loader{position:fixed;inset:0;background:var(--film-deep);z-index:200;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:26px;transition:opacity .8s ease, visibility .8s}
#loader.done{opacity:0;visibility:hidden}
#loader img{width:180px;filter:drop-shadow(0 0 24px rgba(232,163,61,.25))}
#loadbar{width:min(320px,70vw);height:2px;background:rgba(244,243,239,.12);overflow:hidden}
#loadbar i{display:block;height:100%;width:0%;background:linear-gradient(90deg,var(--gold),var(--gold-hot));transition:width .25s ease}
#loadpct{font-family:'IBM Plex Mono',monospace;font-size:11px;letter-spacing:.28em;color:var(--silver)}

/* ============ HEADER ============ */
header{position:fixed;top:0;left:0;right:0;z-index:100;padding:18px clamp(20px,4vw,44px) 18px clamp(22px,6vw,90px);display:flex;align-items:center;justify-content:space-between;color:#fff;transition:color .4s ease}
header::before{content:"";position:absolute;inset:0 0 auto 0;height:190%;z-index:-1;background:linear-gradient(to bottom,rgba(5,8,24,.66),rgba(5,8,24,0));pointer-events:none;transition:opacity .4s ease}
header.on-light{color:var(--ink)}
header.on-light::before{opacity:0}
.brand img{width:min(300px,56vw);filter:drop-shadow(0 2px 18px rgba(0,0,0,.45)) brightness(0) invert(1);transition:filter .4s ease,width .4s ease}
header.on-light .brand img{filter:none;width:128px}
.head-right{display:flex;align-items:center;gap:26px}
.head-phone{font-family:'IBM Plex Mono',monospace;font-size:12px;letter-spacing:.08em;text-shadow:0 1px 16px rgba(0,0,0,.45)}
header.on-light .head-phone{text-shadow:none}
.head-cta{font-family:'Barlow Condensed',sans-serif;font-weight:700;font-style:italic;text-transform:uppercase;letter-spacing:.1em;font-size:15px;padding:10px 22px;border:1px solid currentColor;transition:background .3s,color .3s}
.head-cta:hover{background:var(--gold);border-color:var(--gold);color:var(--ink)}

/* chapter readout */
#readout{position:fixed;left:clamp(20px,4vw,44px);bottom:26px;z-index:90;font-family:'IBM Plex Mono',monospace;font-size:11px;letter-spacing:.24em;text-transform:uppercase;color:#fff;opacity:0;transition:opacity .6s ease;text-shadow:0 1px 12px rgba(0,0,0,.6)}
.btn-gold::after{content:"";position:absolute;top:0;bottom:0;left:-70%;width:40%;background:linear-gradient(100deg,transparent,rgba(255,255,255,.55),transparent);transform:skewX(-18deg);transition:left .55s ease}
.btn-gold:hover::after{left:130%}
.btn-gold:hover{transform:translateY(-2px);box-shadow:0 8px 44px rgba(232,163,61,.5)}
.btn-gold:active{transform:translateY(0)}
.btn-line{display:inline-block;font-family:'Barlow Condensed',sans-serif;font-weight:700;font-style:italic;text-transform:uppercase;letter-spacing:.1em;font-size:19px;padding:16px 34px;border:1px solid rgba(255,255,255,.55);color:#fff;transition:border-color .3s,background .3s}
.btn-line:hover{border-color:var(--gold);background:rgba(232,163,61,.12)}

/* ============ CONTENT ============ */
#content{position:relative;background:var(--ivory);color:var(--ink)}
#landing{height:9vh;background:linear-gradient(to bottom,var(--seam,#3a2c14) 0%,var(--ink) 100%)}
.section{min-height:min(92vh,900px);display:flex;align-items:center;padding:72px clamp(22px,6vw,90px)}
.wrap-c{max-width:1200px;margin:0 auto;width:100%}
.kick-c{font-family:'IBM Plex Mono',monospace;font-size:12px;letter-spacing:.3em;text-transform:uppercase;color:#9a7420;margin-bottom:18px}
.display{font-family:'Barlow Condensed',sans-serif;font-weight:800;font-style:italic;text-transform:uppercase;line-height:.92;letter-spacing:.01em}
.section .wrap-c > *,.section .stag > *{opacity:0;transform:translateY(26px);transition:opacity .9s var(--ease-out),transform .9s var(--ease-out)}
.section.in .wrap-c > *,.section.in .stag > *{opacity:1;transform:none}
.section.in .wrap-c > *:nth-child(2){transition-delay:.09s}
.section.in .wrap-c > *:nth-child(3){transition-delay:.18s}
.section.in .wrap-c > *:nth-child(4){transition-delay:.27s}
.section.in .stag > *:nth-child(2){transition-delay:.12s}
# 搜索工具

Exa AI 搜索引擎。

## Exa AI 搜索

高质量 AI 搜索引擎，擅长技术和代码搜索。

```bash
mcporter call 'exa.web_search_exa(query: "query", numResults: 5)'
mcporter call 'exa.get_code_context_exa(query: "code question", tokensNum: 3000)'
```

### 使用场景

| 场景 | 参数 |
|-----|------|
| 网页搜索 | `web_search_exa(query: "...", numResults: 5)` |
| 代码搜索 | `get_code_context_exa(query: "...", tokensNum: 3000)` |

### 特点

- 擅长英文内容和技术文档
- 支持代码上下文搜索
- 结果质量高

## 与其他搜索工具对比

| 工具 | 来源 | 适用场景 |
|-----|------|---------|
| Exa | agent-reach | 英文/技术/代码搜索 |
| 智谱搜索 | my-mcp-tools | 中文搜索 |
| GitHub 搜索 | agent-reach (dev.md) | 仓库/代码搜索 |

web search: 
web search: site.developer.mozilla.org display table accessibility block grid table ...
exec
/bin/zsh -lc "mcporter call 'exa.web_search_exa(query: \"site.webkit.org SVG CSS custom properties presentation attributes\", numResults: 3)'" in /Users/jc/Desktop/cluade/f2g-longroad
 exited 1 in 239ms:
[mcporter] Unknown MCP server 'exa'.
Error: Unknown MCP server 'exa'.
    at McpRuntime.connect (file:///Users/jc/.npm-global/lib/node_modules/mcporter/dist/runtime.js:184:19)
    at McpRuntime.callTool (file:///Users/jc/.npm-global/lib/node_modules/mcporter/dist/runtime.js:133:43)
    at attemptCall (file:///Users/jc/.npm-global/lib/node_modules/mcporter/dist/cli/call-command.js:299:50)
    at invokeWithAutoCorrection (file:///Users/jc/.npm-global/lib/node_modules/mcporter/dist/cli/call-command.js:295:12)
    at invokePreparedCall (file:///Users/jc/.npm-global/lib/node_modules/mcporter/dist/cli/call-command.js:101:28)
    at handleCall (file:///Users/jc/.npm-global/lib/node_modules/mcporter/dist/cli/call-command.js:21:30)
    at async runCli (file:///Users/jc/.npm-global/lib/node_modules/mcporter/dist/cli.js:136:13)
    at async main (file:///Users/jc/.npm-global/lib/node_modules/mcporter/dist/cli.js:194:5)

web search: 
web search: site.bugs.webkit.org table display grid accessibility table semantics ...
codex
1. **Fix the scroll destination first; extra section padding solves a different problem.**

`header{position:fixed;top:0;…;z-index:100}` overlays the viewport. `.section{…align-items:center;padding:72px …}` does not reserve header space, and `#proof{min-height:0;padding:clamp(48px,6.5vh,78px) …}` reduces its top padding to approximately **52.8px** at 812px height.

For fragment navigation, including the new `<a href="#calc">`, use a shared header offset: `html{scroll-padding-top:var(--header-height)}` or section `scroll-margin-top`. Choose one primary offset to avoid accumulating both. Derive the height from the actual header if it changes with viewport or header state.

These properties **do not change layout or prevent overlap during ordinary scrolling**, and do not correct explicit `window.scrollTo()` coordinates. If your requirement is specifically “content clears the header when the section border is at viewport top,” section top padding must reserve the header height plus clearance. Override `#proof` explicitly because its ID-selector padding wins over `.section`.

There is no necessary structural rewrite. An ordinary in-flow header eliminates persistent overlay; a sticky header still overlays content once stuck. Switching to sticky alone does not fix anchor clearance.

2. **One definite additional accessibility defect; no demonstrated tap-target, contrast, or reflow failure.**

- **Mobile column headers disappear from assistive technology.** `.proof-table thead{display:none}` removes the `<th scope="col">Tested for / Improvement / Type of test</th>` headers from the accessibility tree. The clipped `<caption>` names the table but cannot replace column associations. Preserve accessible headers using visually hidden treatment compatible with the layout. The descriptive first cells, currently `<td class="what proof-what">`, should also become `<th scope="row">` so table navigation can identify each result. [W3C table guidance](https://www.w3.org/WAI/tutorials/tables/)

- **Contrast passes for the new proof text.** Against the actual `--ink:#030D30`, `.proof-src`’s `rgba(244,243,239,.48)` calculates to approximately **4.54:1**, narrowly passing normal-text AA. Header text at `.55` is **5.63:1**; test/footnote text at `.6` is **6.52:1** on the plain background. The highlighted row also retains sufficient text contrast. Small font size alone is not a contrast failure.

- **The new link is not a target-size violation.** `<a href="#calc">savings calculator</a>` is embedded in a sentence and qualifies for the inline exception. [WCAG target-size guidance](https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum)

- **No demonstrated reflow defect.** `minmax(0,1fr) auto`, wrapping labels, and `.wrap-c{min-width:0}` support the mobile layout. Moving `.test` below `.what` leaves the DOM sequence “label, value, test” meaningful. Your 375px measurement does not establish 320px or enlarged-text behavior, but it provides no basis to claim overflow.

3. **No confirmed iOS-specific rendering break from this diff.**

`fill="var(--t-chassis)"`, `stroke="var(--t-band)"`, and `stop-color="var(--t-barrel-hi)"` use inherited custom properties defined on `#how`; their presence alone is not evidence of Safari failure. WebKit supports CSS custom properties. [WebKit documentation](https://webkit.org/blog/5989/css-variables-in-webkit/)

Changing native table display to `block` and rows to `grid` warrants a Safari/VoiceOver check: browser accessibility mappings can change with these declarations. That is a compatibility risk, **not a reproduced iOS defect**. [MDN’s accessibility warning](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/display#accessibility)

The hidden-header defect above is certain and cross-browser. The truck is already inside `.run-road[aria-hidden="true"]`, so its lack of an accessible SVG name is appropriate. I did not execute an iOS Safari test.
tokens used
30,871
1. **Fix the scroll destination first; extra section padding solves a different problem.**

`header{position:fixed;top:0;…;z-index:100}` overlays the viewport. `.section{…align-items:center;padding:72px …}` does not reserve header space, and `#proof{min-height:0;padding:clamp(48px,6.5vh,78px) …}` reduces its top padding to approximately **52.8px** at 812px height.

For fragment navigation, including the new `<a href="#calc">`, use a shared header offset: `html{scroll-padding-top:var(--header-height)}` or section `scroll-margin-top`. Choose one primary offset to avoid accumulating both. Derive the height from the actual header if it changes with viewport or header state.

These properties **do not change layout or prevent overlap during ordinary scrolling**, and do not correct explicit `window.scrollTo()` coordinates. If your requirement is specifically “content clears the header when the section border is at viewport top,” section top padding must reserve the header height plus clearance. Override `#proof` explicitly because its ID-selector padding wins over `.section`.

There is no necessary structural rewrite. An ordinary in-flow header eliminates persistent overlay; a sticky header still overlays content once stuck. Switching to sticky alone does not fix anchor clearance.

2. **One definite additional accessibility defect; no demonstrated tap-target, contrast, or reflow failure.**

- **Mobile column headers disappear from assistive technology.** `.proof-table thead{display:none}` removes the `<th scope="col">Tested for / Improvement / Type of test</th>` headers from the accessibility tree. The clipped `<caption>` names the table but cannot replace column associations. Preserve accessible headers using visually hidden treatment compatible with the layout. The descriptive first cells, currently `<td class="what proof-what">`, should also become `<th scope="row">` so table navigation can identify each result. [W3C table guidance](https://www.w3.org/WAI/tutorials/tables/)

- **Contrast passes for the new proof text.** Against the actual `--ink:#030D30`, `.proof-src`’s `rgba(244,243,239,.48)` calculates to approximately **4.54:1**, narrowly passing normal-text AA. Header text at `.55` is **5.63:1**; test/footnote text at `.6` is **6.52:1** on the plain background. The highlighted row also retains sufficient text contrast. Small font size alone is not a contrast failure.

- **The new link is not a target-size violation.** `<a href="#calc">savings calculator</a>` is embedded in a sentence and qualifies for the inline exception. [WCAG target-size guidance](https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum)

- **No demonstrated reflow defect.** `minmax(0,1fr) auto`, wrapping labels, and `.wrap-c{min-width:0}` support the mobile layout. Moving `.test` below `.what` leaves the DOM sequence “label, value, test” meaningful. Your 375px measurement does not establish 320px or enlarged-text behavior, but it provides no basis to claim overflow.

3. **No confirmed iOS-specific rendering break from this diff.**

`fill="var(--t-chassis)"`, `stroke="var(--t-band)"`, and `stop-color="var(--t-barrel-hi)"` use inherited custom properties defined on `#how`; their presence alone is not evidence of Safari failure. WebKit supports CSS custom properties. [WebKit documentation](https://webkit.org/blog/5989/css-variables-in-webkit/)

Changing native table display to `block` and rows to `grid` warrants a Safari/VoiceOver check: browser accessibility mappings can change with these declarations. That is a compatibility risk, **not a reproduced iOS defect**. [MDN’s accessibility warning](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/display#accessibility)

The hidden-header defect above is certain and cross-browser. The truck is already inside `.run-road[aria-hidden="true"]`, so its lack of an accessible SVG name is appropriate. I did not execute an iOS Safari test.
