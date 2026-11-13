---
layout: default
title: Portfolio
---

<section class="intro" aria-labelledby="intro-title">
  <div class="intro-heading">
    <p class="eyebrow">Full-stack developer · Germany</p>
    <h1 id="intro-title">Web products &amp;<br>developer tools<span>.</span></h1>
  </div>
  <div class="intro-note">
    <img src="{{ '/assets/img/profile_icon.png' | relative_url }}" alt="Jinyao Chen" width="64" height="64" class="intro-photo">
    <p>I'm Jinyao. I build independent web products, agentic AI infrastructure, and open-source tools.<br><a href="#about">A little more about me <span aria-hidden="true">↗</span></a></p>
  </div>
</section>

<section id="work" class="work-section" aria-labelledby="work-title">
  <div class="section-heading">
    <h2 id="work-title">Selected work</h2>
    <p>Independent projects, live on the web</p>
  </div>
  <div class="projects-grid">
    {% for project in site.data.projects %}
      {% include project-card.html project=project %}
    {% endfor %}
  </div>
</section>

<section id="open-source" class="page-section" aria-labelledby="open-source-title">
  <div class="section-heading">
    <div>
      <p class="eyebrow">Built for developers</p>
      <h2 id="open-source-title">Open-source tools</h2>
    </div>
    <a class="text-link" href="https://github.com/cjy513203427" target="_blank" rel="noopener noreferrer">More on GitHub<span class="sr-only"> (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
  </div>
  <div class="tools-grid">
    <article class="tool-card">
      <div class="tool-heading"><span class="tool-symbol" aria-hidden="true">&gt;_</span><span class="project-category">MCP server</span></div>
      <h3>ObjectScript Routine MCP</h3>
      <p>Connect AI agents to InterSystems IRIS. An MCP server for inspecting, reading, and managing server routines from tools like Claude and Cursor.</p>
      <div class="tool-links">
        <a class="text-link" href="https://www.npmjs.com/package/intersystems-objectscript-routine-mcp" target="_blank" rel="noopener noreferrer">View on npm<span class="sr-only"> (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
        <a class="secondary-link" href="https://github.com/cjy513203427/intersystems-objectscript-routine-mcp" target="_blank" rel="noopener noreferrer">Source code<span class="sr-only"> (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
      </div>
    </article>
    <article class="tool-card">
      <div class="tool-heading"><span class="tool-symbol" aria-hidden="true">{ }</span><span class="project-category">VS Code extension</span></div>
      <h3>ObjectScript Class Diagram View</h3>
      <p>Turn InterSystems ObjectScript class structures into interactive diagrams. Generate UML views and navigate relationships to understand an existing codebase.</p>
      <div class="tool-links">
        <a class="text-link" href="https://marketplace.visualstudio.com/items?itemName=JinyaoChen.intersystems-objectscript-class-diagram-view" target="_blank" rel="noopener noreferrer">VS Code Marketplace<span class="sr-only"> (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
      </div>
    </article>
  </div>
  <div class="contributions">
    <p>Also contributing to</p>
    <a href="https://github.com/open-edge-platform/anomalib/pulls?q=is%3Apr+author%3Acjy513203427" target="_blank" rel="noopener noreferrer">Anomalib <span class="contribution-description">· Anomaly detection</span><span class="sr-only"> contributions (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
    <a href="https://github.com/intersystems-community/vscode-objectscript" target="_blank" rel="noopener noreferrer">VSCode ObjectScript<span class="sr-only"> (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
  </div>
</section>

<section id="research" class="page-section" aria-labelledby="research-title">
  <div class="section-heading">
    <div>
      <p class="eyebrow">From research to implementation</p>
      <h2 id="research-title">Research &amp; experiments</h2>
    </div>
    <a class="secondary-link" href="https://scholar.google.com/citations?user=NfZXs0sAAAAJ&amp;hl" target="_blank" rel="noopener noreferrer">Google Scholar<span class="sr-only"> (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
  </div>
  <div class="research-grid">
    <article class="research-project">
      <p class="project-category">Master's thesis · 2023–2024</p>
      <h3>Industrial Anomaly Detection Benchmark Engine</h3>
      <p>A reproducible platform for comparing anomaly detection methods in industrial applications, with a Python engine, Spring Boot backend, and Angular frontend.</p>
      <a class="text-link" href="https://github.com/cjy513203427/IADBE" target="_blank" rel="noopener noreferrer">Explore IADBE<span class="sr-only"> (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
    </article>
    <article class="research-project">
      <p class="project-category">AI &amp; Machine Learning Lab · 2023</p>
      <h3>Multi Policy Value Monte Carlo Tree Search</h3>
      <p>A PyTorch implementation combining a large feature extraction network with a smaller search network and a meta-learner to guide the MCTS process.</p>
      <a class="text-link" href="https://github.com/cjy513203427/CrazyAra/tree/mpv_mcts" target="_blank" rel="noopener noreferrer">View implementation<span class="sr-only"> (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
    </article>
    <article class="research-project">
      <p class="project-category">Multimedia Communication Lab · 2024</p>
      <h3>Adaptive 360° Video Streaming</h3>
      <p>Performance-aware switching between 360° and 90° streaming modes, using latency, encoding and decoding time, and frame rate to adapt video quality.</p>
      <p class="research-context">TU Darmstadt · Lab project</p>
    </article>
  </div>
</section>

<section id="about" class="page-section about-section" aria-labelledby="about-title">
  <div class="about-intro">
    <p class="eyebrow">The person behind the projects</p>
    <h2 id="about-title">Hi, I'm Jinyao.</h2>
    <p>I'm a full-stack developer with an MSc in Computer Science from TU Darmstadt. My work connects industrial software, AI/ML research, and practical developer tools.</p>
    <p>I'm exploring agentic AI and the MCP ecosystem, alongside compilers, CUDA, and Linux kernel optimization. Away from the keyboard: breaking, guitar, and art.</p>
    <div class="about-links">
      <a class="text-link" href="mailto:{{ site.email }}">Email me <span aria-hidden="true">↗</span></a>
      <a class="secondary-link" href="https://www.linkedin.com/in/jinyao-chen-8b5b29225/" target="_blank" rel="noopener noreferrer">LinkedIn<span class="sr-only"> (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
      <a class="secondary-link" href="https://github.com/cjy513203427" target="_blank" rel="noopener noreferrer">GitHub<span class="sr-only"> (opens in a new tab)</span><span aria-hidden="true">↗</span></a>
    </div>
    <div class="languages">
      <h3>Languages</h3>
      <ul><li>Chinese · Native</li><li>English &amp; German · C1</li><li>Japanese · B2</li><li>Korean &amp; Russian · A1</li></ul>
    </div>
  </div>
  <div class="background">
    <h3 class="background-heading">Experience</h3>
    <details class="experience">
      <summary><span><strong>Full Stack Developer</strong><span class="experience-company">EXOR PRO GmbH · Ilmenau</span></span><span class="experience-date">Nov 2024 – Present<span class="details-toggle" aria-hidden="true"></span></span></summary>
      <ul>
        <li>Enterprise applications with InterSystems ObjectScript, GraphQL, and Angular.</li>
        <li>AI-supported ticket system using RAG and LLMs for customer support.</li>
        <li>Legacy system migration to NestJS and monitoring with Grafana and Prometheus.</li>
      </ul>
    </details>
    <details class="experience">
      <summary><span><strong>Java Developer</strong><span class="experience-company">TRADUI Technologies GmbH · Frankfurt</span></span><span class="experience-date">Jan 2022 – Jun 2023<span class="details-toggle" aria-hidden="true"></span></span></summary>
      <ul>
        <li>Full-stack office automation systems, database design, and report generation.</li>
        <li>Eclipse plugin development for automated reporting.</li>
      </ul>
    </details>
    <details class="experience">
      <summary><span><strong>Java Big Data Developer</strong><span class="experience-company">Finedo Co. Ltd · Hefei</span></span><span class="experience-date">Oct 2018 – Oct 2019<span class="details-toggle" aria-hidden="true"></span></span></summary>
      <ul>
        <li>Financial big data platforms using Hadoop, HBase, Hive, and Storm.</li>
        <li>High-concurrency system design with message queues.</li>
      </ul>
    </details>
    <h3 class="background-heading education-heading">Education</h3>
    <div class="education-item">
      <div><h4>MSc Computer Science</h4><span class="experience-date">2020–2024</span></div>
      <p>Technische Universität Darmstadt, Germany</p>
      <p class="thesis">Thesis: Evaluation of modern methods for detecting anomalies in industry</p>
    </div>
    <div class="education-item">
      <div><h4>BSc Computer Science</h4><span class="experience-date">2014–2018</span></div>
      <p>Anhui Institute of Information Technology, China</p>
    </div>
  </div>
</section>

<section class="skills-section" aria-labelledby="skills-title">
  <h2 id="skills-title">What I work with</h2>
  <div class="skills-grid">
    <div><h3>Languages &amp; backend</h3><p>Java, Python, TypeScript, JavaScript, C#, ObjectScript, Spring, NestJS, Django, .NET Core</p></div>
    <div><h3>Frontend</h3><p>Angular, React, Vue, ExtJS</p></div>
    <div><h3>AI &amp; machine learning</h3><p>PyTorch, TensorFlow, Keras, Hugging Face Transformers, OpenCV</p></div>
    <div><h3>Infrastructure</h3><p>Docker, Kubernetes, GitLab CI/CD, Jenkins, Hadoop, HBase, Hive, Storm</p></div>
  </div>
  <p class="other-interests">Also familiar with Unity, Unreal Engine, CryEngine, and system programming.</p>
</section>

<section id="contact" class="contact-section" aria-labelledby="contact-title">
  <div><p class="eyebrow">Have a project in mind?</p><h2 id="contact-title">Let's build something useful.</h2></div>
  <a class="contact-button" href="mailto:{{ site.email }}">{{ site.email }} <span aria-hidden="true">↗</span></a>
</section>
