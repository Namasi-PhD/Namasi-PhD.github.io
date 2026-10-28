---
layout: single
title: "CV"
permalink: /cv/
wide: true
lede: "Read it here, or grab the PDF."
---

<div class="cv-toolbar">
  <a class="btn btn-primary" href="/CV.pdf" download="Namasi_G_Sankar_CV.pdf">Download PDF</a>
  <a class="btn" href="/CV.pdf" target="_blank" rel="noopener">Open in new tab</a>
</div>

<div class="cv-pages" id="cv-pages" aria-label="CV pages">
  <div class="cv-skel"></div>
</div>
<noscript><p class="cv-status">JavaScript is off — <a href="/CV.pdf">open the CV as a PDF</a>.</p></noscript>

<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
<script>
(function(){
  var holder = document.getElementById('cv-pages');
  var url = '/CV.pdf';
  function fail(){
    holder.innerHTML = '<p class="cv-status">The preview could not load here. <a href="' + url + '">Open the CV as a PDF</a>.</p>';
  }
  if (!window.pdfjsLib) { fail(); return; }
  pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';
  var doc = null, lastW = 0, busy = false;
  function render(){
    if (!doc || busy) return;
    var w = holder.clientWidth;
    if (!w || Math.abs(w - lastW) < 40) return;
    busy = true; lastW = w;
    var dpr = Math.min(window.devicePixelRatio || 1, 2.5);
    var canvases = [];
    var chain = Promise.resolve();
    for (var i = 1; i <= doc.numPages; i++) {
      (function(n){
        chain = chain.then(function(){ return doc.getPage(n); }).then(function(page){
          var base = page.getViewport({ scale: 1 });
          var vp = page.getViewport({ scale: (w / base.width) * dpr });
          var c = document.createElement('canvas');
          c.width = Math.floor(vp.width); c.height = Math.floor(vp.height);
          c.setAttribute('role', 'img'); c.setAttribute('aria-label', 'CV page ' + n);
          canvases.push(c);
          return page.render({ canvasContext: c.getContext('2d'), viewport: vp, intent: 'print' }).promise;
        });
      })(i);
    }
    chain.then(function(){
      holder.innerHTML = '';
      canvases.forEach(function(c){ holder.appendChild(c); });
      busy = false;
    }).catch(function(){ busy = false; fail(); });
  }
  pdfjsLib.getDocument(url).promise.then(function(d){
    doc = d;
    var sk = '';
    for (var i = 1; i < d.numPages; i++) sk += '<div class="cv-skel"></div>';
    holder.insertAdjacentHTML('beforeend', sk);
    render();
  }).catch(fail);
  var t;
  window.addEventListener('resize', function(){ clearTimeout(t); t = setTimeout(render, 250); });
})();
</script>
