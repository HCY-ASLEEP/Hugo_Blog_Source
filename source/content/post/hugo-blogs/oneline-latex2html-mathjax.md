---
title: "Online Latex2html Converter By Mathjax"
date: 2024-11-08T14:44:13Z
draft: false
---

<h3>Enter LaTeX Formula 👇</h3>
<div style="border: 1px solid gray; border-radius: 10px">
  <textarea id="latex-input" placeholder="e.g. E = mc^2" style="margin: 10px"></textarea>
</div>
<div id="output"></div>
<h3>Generated HTML Code 👇</h3>
<div style="min-height: 50px; border: 1px solid gray; border-radius: 10px">
  <div id="html-code" style="margin: 10px"></div>
</div>
<div style="border-radius: 10px; background-color: light-green; width: auto; margin: 10px">
  <button id="copy-button">🖱️👉 just click here to copy html code 👈</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
<script>
  document.getElementById("latex-input").addEventListener("input", function () {
    const latex = document.getElementById("latex-input").value;
    const output = document.getElementById("output");
    const htmlCode = document.getElementById("html-code");

    output.innerHTML = '\\(' + latex + '\\)';
    MathJax.typesetPromise([output]).then(() => {
      htmlCode.textContent = output.innerHTML;
    }).catch((err) => console.error(err));
  });

  document.getElementById("copy-button").addEventListener("click", function () {
    const htmlCode = document.getElementById("html-code").textContent;

    navigator.clipboard.writeText(htmlCode).then(() => {
      alert("HTML code copied to clipboard!");
    }).catch((err) => {
      console.error("Failed to copy text: ", err);
    });
  });
</script>