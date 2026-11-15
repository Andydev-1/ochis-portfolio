<script setup>
import { ref, onMounted, onBeforeUnmount, nextTick } from "vue";

const navOpen = ref(false);
const portrait = new URL("./assets/img/p16.jpeg", import.meta.url).href;
const closeMenuOnEscape = (event) => {
  if (event.key === "Escape" && navOpen.value) {
    navOpen.value = false;
    document.getElementById("menu-toggle")?.focus();
  }
};

const brandLogos = [
  new URL("./assets/img/log1.jpg", import.meta.url).href,
  new URL("./assets/img/p22.jpeg", import.meta.url).href,
  new URL("./assets/img/p23.jpeg", import.meta.url).href,
];

const focusStats = [
  { label: "Years of academic and research experience", value: "04" },
  { label: "Awaiting publications / projects", value: "1+" },
  { label: "Certifications featured", value: "07" },
];

const impactStats = [
  { value: "240", label: "Students reached through a self-led SDG project" },
  { value: "100", label: "Children supported through BELLZ Africa’s school outreach" },
  { value: "10", label: "Team members on a school first-aid initiative" },
];

const skillSets = [
  {
    category: "Core Skills",
    items: [
      {
        text: "Research skills (qualitative & quantitative) – currently implemented at Abuja School of Social and Political Thought and the International Institute of SDG and Public Policy (online researcher)",
        icon: "📊",
      },
      { text: "Microsoft Office Suite", icon: "💻" },
      { text: "Problem Solving Skills", icon: "🧩" },
      { text: "Analytical Skills", icon: "🧠" },
    ],
  },
];

const certifications = [
  {
    title: "UN Security Council Resolution 1540 (2004)",
    description: "Understanding global non-proliferation frameworks",
    issuer: "United Nations",
    year: "2025",
    asset: new URL("./assets/img/cert1.pdf", import.meta.url).href,
    format: "pdf",
  },
  {
    title: "Global Diplomacy",
    description:
      "Exploring multilateral negotiation and international relations strategies",
    issuer: "Diplomatic Training Institute",
    year: "2025",
    asset: new URL("./assets/img/cert2.pdf", import.meta.url).href,
    format: "pdf",
  },
  {
    title: "Gender Sensitivity in Construction",
    description:
      "Gaining insights into inclusive practices in professional settings",
    issuer: "Professional Development Council",
    year: "2025",
    asset: new URL("./assets/img/cert3.pdf", import.meta.url).href,
    format: "pdf",
  },
  {
    title: "International Aid and Humanitarian Response Training",
    description: "Introduction to Humanitarian Aid and Training",
    issuer: "Alison",
    year: "2025",
    asset: new URL("./assets/img/cert4.jpg", import.meta.url).href,
    format: "image",
  },
  {
    title: "Understanding International Security",
    description:
      "Comprehensive course on international security frameworks and strategies",
    issuer: "Security Studies Institute",
    year: "2025",
    asset: new URL("./assets/img/cert5.jpg", import.meta.url).href,
    format: "image",
  },
  {
    title: "Advanced Policy Analysis",
    description: "Strategic policy formulation and analysis methodologies",
    issuer: "Policy Research Academy",
    year: "2025",
    asset: new URL("./assets/img/cert6.pdf", import.meta.url).href,
    format: "pdf",
  },
  {
    title: "Peace and Conflict Resolution",
    description: "Foundations of peacebuilding and conflict transformation",
    issuer: "Peace Studies Institute",
    year: "2025",
    asset: new URL("./assets/img/cert7.pdf", import.meta.url).href,
    format: "pdf",
  },
];

const certificationMarquee = [...certifications, ...certifications];

const projects = [
  {
    title: "Bellz Africa back to school project",
    description:
      "An outreach project aimed at reducing educational barriers by equipping students with school supplies, mentorship, and motivation to begin the academic year confidently.",
    image: new URL("./assets/img/p11.jpg", import.meta.url).href,
    tags: ["Outreach", "Community", "Education"],
  },
  {
    title: "National Disability Summit",
    description:
      "A summit aimed at raising awareness about the rights of persons with disabilities and promoting their inclusion in society.",
    image: new URL("./assets/img/p2.jpg", import.meta.url).href,
    tags: ["Summit", "Disability", "Inclusion"],
  },
  {
    title: "Bellz Africa back to school project",
    description:
      "A community-driven initiative providing educational support, learning materials, and encouragement to young students returning to school across local communities.",
    image: new URL("./assets/img/p10.jpg", import.meta.url).href,
    tags: ["Outreach", "Community", "Education"],
  },
  {
    title: "National disability summit day1",
    description:
      "A documentation project capturing powerful moments, faces, and stories from the National Disability Summit.",
    image: new URL("./assets/img/p4.jpg", import.meta.url).href,
    tags: ["Summit", "Disability", "Inclusion"],
  },
  {
    title: "National disability summit day2",
    description:
      "A visual coverage project focused on documenting participation, representation, and impactful interactions throughout the National Disability Summit.",
    image: new URL("./assets/img/p5.jpg", import.meta.url).href,
    tags: ["Summit", "Disability", "Inclusion"],
  },
  {
    title: "National disability summit day3",
    description:
      "A session focused on promoting workplace inclusivity through legal rights, organizational policies, and best practices that support employees with disabilities and foster equal participation.",
    image: new URL("./assets/img/p6.jpg", import.meta.url).href,
    tags: ["Rights", "Benefits", "Inclusion"],
  },
  {
    title: "self led sustainable development project",
    description:
      "A continuation of the outreach project aimed at reducing educational barriers by equipping students with school supplies, mentorship, and motivation to begin the academic year confidently.",
    image: new URL("./assets/img/p7.jpg", import.meta.url).href,
    tags: ["Outreach", "Community", "Education"],
  },
  {
    title: "self led sustainable development project cont.",
    description:
      "A continuation of the outreach project aimed at reducing educational barriers by equipping students with school supplies, mentorship, and motivation to begin the academic year confidently.",
    image: new URL("./assets/img/p8.jpg", import.meta.url).href,
    tags: ["Outreach", "Community", "Education"],
  },
  {
    title: "Ochanya during a federal road safety commission outreach",
    description:
      "Captured during an FRSC outreach where Ochanya supported community sensitization on road safety, responsible driving, and public awareness.",
    image: new URL("./assets/img/p12.jpg", import.meta.url).href,
    tags: ["Outreach", "Community", "Education"],
  },
];

const certMarqueeRef = ref(null);

const setupMarqueeInteractions = (containerRef, speed = 0.4) => {
  const el = containerRef.value;
  if (!el) return () => {};

  let rafId;
  let isPaused = false;
  let isDragging = false;
  let startX = 0;
  let startScrollLeft = 0;

  const normalizeScroll = () => {
    const half = el.scrollWidth / 2;
    if (!half) return;
    while (el.scrollLeft >= half) {
      el.scrollLeft -= half;
    }
    while (el.scrollLeft < 0) {
      el.scrollLeft += half;
    }
  };

  const autoScroll = () => {
    if (!isPaused && !isDragging) {
      el.scrollLeft += speed;
      normalizeScroll();
    }
    rafId = requestAnimationFrame(autoScroll);
  };

  const onPointerDown = (event) => {
    isDragging = true;
    isPaused = true;
    startX = event.clientX;
    startScrollLeft = el.scrollLeft;
    el.classList.add("is-dragging");
    el.setPointerCapture?.(event.pointerId);
  };

  const onPointerMove = (event) => {
    if (!isDragging) return;
    const delta = event.clientX - startX;
    el.scrollLeft = startScrollLeft - delta;
    normalizeScroll();
  };

  const releaseDrag = (event) => {
    if (!isDragging) return;
    isDragging = false;
    el.classList.remove("is-dragging");
    el.releasePointerCapture?.(event.pointerId);
    startScrollLeft = el.scrollLeft;
    setTimeout(() => {
      isPaused = false;
    }, 600);
  };

  const onPointerCancel = (event) => {
    if (!isDragging) return;
    releaseDrag(event);
  };

  const onMouseEnter = () => {
    isPaused = true;
  };

  const onMouseLeave = (event) => {
    if (isDragging) {
      releaseDrag(event);
    }
    isPaused = false;
  };

  el.addEventListener("pointerdown", onPointerDown);
  el.addEventListener("pointermove", onPointerMove);
  el.addEventListener("pointerup", releaseDrag);
  el.addEventListener("pointercancel", onPointerCancel);
  el.addEventListener("mouseenter", onMouseEnter);
  el.addEventListener("mouseleave", onMouseLeave);

  rafId = requestAnimationFrame(autoScroll);

  return () => {
    cancelAnimationFrame(rafId);
    el.removeEventListener("pointerdown", onPointerDown);
    el.removeEventListener("pointermove", onPointerMove);
    el.removeEventListener("pointerup", releaseDrag);
    el.removeEventListener("pointercancel", onPointerCancel);
    el.removeEventListener("mouseenter", onMouseEnter);
    el.removeEventListener("mouseleave", onMouseLeave);
    el.classList.remove("is-dragging");
  };
};

const cleanupFns = [];

onMounted(() => {
  document.addEventListener("keydown", closeMenuOnEscape);
  nextTick(() => {
    cleanupFns.push(setupMarqueeInteractions(certMarqueeRef, 0.35));
  });
});

onBeforeUnmount(() => {
  document.removeEventListener("keydown", closeMenuOnEscape);
  cleanupFns.forEach((stop) => stop && stop());
});

const socialLinks = [
  {
    label: "Email",
    value: "Ikpaochanya60@gmail.com",
    href: "mailto:Ikpaochanya60@gmail.com",
  },
  { label: "instagram", value: "@essentials_reo4" },
  {
    label: "LinkedIn",
    value: "/in/ikpa-ochanya",
    href: "https://www.linkedin.com/in/ikpa-euginia-27480430b?utm_source=share_via&utm_content=profile&utm_medium=member_android",
  },
];
</script>

<template>
  <div class="page">
    <a class="skip-link" href="#main-content">Skip to content</a>
      <nav class="nav" aria-label="Main navigation" :class="{ 'nav--mobile-open': navOpen }">
        <a class="brand" href="#home" @click="navOpen = false" aria-label="Ikpa Ochanya Euginia — home">
          <span class="brand-mark">IOE</span>
          <span>Ochanya Ikpa<span class="brand-caption">International Relations & Development</span></span>
        </a>
        <button id="menu-toggle" class="menu-toggle" type="button" :aria-expanded="navOpen" aria-controls="navigation-links" @click="navOpen = !navOpen">{{ navOpen ? 'Close' : 'Menu' }} <span aria-hidden="true">{{ navOpen ? '−' : '+' }}</span></button>
        <div id="navigation-links" class="nav-links" :class="{ show: navOpen }">
          <a href="#about" @click="navOpen = false">About</a>
          <a href="#volunteer" @click="navOpen = false">Experience</a>
          <a href="#skills" @click="navOpen = false">Skills</a>
          <a href="#certifications" @click="navOpen = false">Certifications</a>
          <a href="#projects" @click="navOpen = false">Impact</a>
          <a href="#contact" class="pill" @click="navOpen = false"
            >Let’s Talk</a
          >
        </div>
      </nav>
    <main id="main-content" tabindex="-1">
    <header class="hero" id="home">
      <div class="hero-content">
        <p class="eyebrow">
          International Relations Analyst · Global Policy Storyteller
        </p>
        <h1>
          IKPA OCHANYA EUGINIA
          <span
            >Turning global insights into impactful policy and peacebuilding
            strategies.</span
          >
        </h1>
        <p class="intro">
          <strong>International Relations graduate exploring the connections between research, governance, and community-led development.</strong>
          Every global issue carries a story, and I am passionate about
          uncovering the patterns, voices, and policies behind them. Through
          critical research and a commitment to evidence-based thinking, I
          explore how diplomacy, cooperation, and innovative solutions can
          transform uncertainty into resilience and conflict into opportunity.
        </p>
        <div class="hero-cta">
          <a class="primary" href="#projects">Explore my impact <span aria-hidden="true">↗</span></a>
          <a class="ghost" href="#contact">Get in touch <span aria-hidden="true">→</span></a>
        </div>
        <div class="hero-focus" aria-label="Professional interests">
          <span>Research & policy</span><span>Peace & security</span><span>Sustainable development</span>
        </div>
      </div>
      <figure class="hero-portrait">
        <img :src="portrait" alt="Ochanya Ikpa holding her academic research project" width="810" height="1080" fetchpriority="high" decoding="async" />
        <figcaption><span>Global perspective.</span><span>Community purpose.</span></figcaption>
      </figure>
    </header>

    <section class="impact-summary" aria-label="Community impact at a glance">
      <p class="eyebrow small">Purpose in practice</p>
      <div class="impact-stats">
        <div v-for="stat in impactStats" :key="stat.label"><p class="value">{{ stat.value }}</p><p>{{ stat.label }}</p></div>
      </div>
    </section>

    <section class="trusted" aria-label="Personal and professional moments">
      <p class="eyebrow small">In the field & along the way</p>
      <div class="logo-row">
        <img
          v-for="(logo, index) in brandLogos"
          :key="index"
          :src="logo"
          :alt="['Ochanya supporting event coordination', 'Ochanya during her academic journey', 'Ochanya with her completed research project'][index]"
          loading="lazy"
          decoding="async"
        />
      </div>
    </section>

    <section class="about-grid" id="about" aria-labelledby="about-title">
      <article class="card narrative">
        <p class="eyebrow small">01 / The person behind the work</p>
        <h2 id="about-title">About Me</h2>
        <p>
          I am an International Relations graduate with four years of academic
          and research experience, passionate about understanding and addressing
          complex global challenges. My expertise spans policy analysis,
          academic research, and cross-cultural communication, which I apply to
          exploring how public policy, global cooperation, and sustainable
          development intersect to create meaningful change.
        </p>
        <p>
          Beyond policy, I am
          deeply interested in global peace and security strategies,
          humanitarian intervention, and conflict resolution. I aim to
          understand how multilateral engagement, strategic diplomacy, and
          collaborative approaches can prevent conflict, support vulnerable
          populations, and promote stability worldwide.
        </p>
        <p>
          Driven by a commitment
          to evidence-based solutions, I strive to apply global perspectives to
          strengthen policy formulation, peacebuilding, and development
          initiatives within my locality and beyond. My goal is to contribute to
          a world where effective governance, humanitarian action, and
          sustainable growth work hand in hand.
        </p>
        <ul class="focus-list">
          <li>Analyzes global policy to drive sustainable solutions.</li>
          <li>
            Contributed to research and peacebuilding initiatives across
            multiple regions.
          </li>
          <li>Bridges data-driven insights with strategic diplomacy.</li>
        </ul>
      </article>
      <article class="card highlight">
        <h3>Experience Highlights</h3>
        <div class="stat-block" v-for="stat in focusStats" :key="stat.label">
          <p class="value">{{ stat.value }}</p>
          <p class="label">{{ stat.label }}</p>
        </div>
        <p class="note">
          I continuously build my expertise through professional courses and
          specialized training in policy, peace and security, and research.
        </p>
      </article>
    </section>

    <section class="volunteer-experience" id="volunteer">
      <div class="section-heading">
        <p class="eyebrow small">Community Impact</p>
        <h2>Volunteer and Project Experience</h2>
        <p>
          Combining academic knowledge with hands-on community engagement to
          create tangible, meaningful change.
        </p>
      </div>
      <div class="volunteer-content">
        <article class="card volunteer-card">
          <p>
            Over the years, I have actively contributed to projects that have
            strengthened my project management, coordination, and community
            engagement skills. I volunteered with
            <strong>BELLZ Africa's Back-to-School Project</strong>, helping send
            100 children back to school and supporting grassroots education
            initiatives. I also contributed to the
            <strong>National Disability Summit</strong>, working closely with
            the security team to ensure a safe and well-organized event.
          </p>
          <p>
            During my service year, I have been volunteering with the
            <strong>Federal Road Safety Commission</strong>, promoting road
            safety awareness and preventive measures within communities. My
            passion for research and policy led to an invitation by
            <strong>Dr. Sam Amadi</strong> to volunteer at the
            <strong>Abuja School of Social and Political Thought</strong>, where
            I contributed to research papers and engaged in policy discussions.
          </p>
          <p>
            In addition to collaborative projects, I have led initiatives,
            including a project with a team of 10 that provided two fully
            stocked first aid boxes to a school clinic. Most recently, I
            implemented a self-led sustainable development project, reaching
            <strong>240 students</strong> with educational materials and
            lectures to raise awareness about the
            <strong>Sustainable Development Goals (SDGs)</strong>.
          </p>
          <p>
            Through these experiences, I have developed strong leadership,
            planning, and advocacy skills, while applying my academic knowledge
            to create tangible community impact.
          </p>
        </article>
      </div>

      <div class="awards-subsection">
        <div class="awards-heading">
          <h3>Awards and Recognitions</h3>
        </div>
        <div class="awards-grid">
          <div class="award-item">
            <span class="award-icon">🏆</span>
            <div class="award-content">
              <h4>Best Delegate</h4>
              <p>
                United Nations Simulation Summit, Airforce Institute of
                Technology
              </p>
              <span class="award-year">2023</span>
            </div>
          </div>
          <div class="award-item">
            <span class="award-icon">🎓</span>
            <div class="award-content">
              <h4>Best Graduating Student</h4>
              <p>
                International Relations Department, Airforce Institute of
                Technology
              </p>
              <span class="award-year">2024</span>
            </div>
          </div>
          <div class="award-item">
            <span class="award-icon">🎵</span>
            <div class="award-content">
              <h4>Most Dedicated Chorister</h4>
              <p>Nigeria Federation of Catholic Students, AFIT Chapter</p>
              <span class="award-year">2024</span>
            </div>
          </div>
          <div class="award-item">
            <span class="award-icon">💬</span>
            <div class="award-content">
              <h4>Overall Best Debater</h4>
              <p>Jigawa State, National Youth Service Corps</p>
              <span class="award-year">2025</span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section class="skills" id="skills">
      <div class="section-heading">
        <p class="eyebrow small">Expertise</p>
        <h2>Professional & Research Skills</h2>
        <p>
          Practical, analytical, and digital skills developed and demonstrated
          through real research and international policy experience.
        </p>
      </div>
      <div class="skills-grid">
        <div
          v-for="item in skillSets[0].items"
          :key="item.text"
          class="skill-card"
        >
          <span class="skill-icon-badge" aria-hidden="true">{{ String(skillSets[0].items.indexOf(item) + 1).padStart(2, '0') }}</span>
          <span class="skill-card-text">{{ item.text }}</span>
        </div>
      </div>
    </section>

    <section class="certifications" id="certifications">
      <div class="section-heading">
        <p class="eyebrow small">Professional Development</p>
        <h2>Certifications</h2>
        <p>
          To enhance my professional knowledge and practical skills, I have
          completed several certifications and courses that complement my
          academic background in International Relations and strengthen my
          ability to contribute effectively to policy analysis, peace and
          security initiatives, humanitarian interventions, and
          community-focused projects.
        </p>
      </div>
      <div class="marquee cert-marquee" ref="certMarqueeRef">
        <div class="marquee-track">
          <article
            v-for="(cert, index) in certificationMarquee"
            :key="cert.title + index"
            class="cert-card"
          >
            <div class="cert-media">
              <img
                v-if="cert.format === 'image'"
                :src="cert.asset"
                :alt="cert.title"
              />
              <iframe
                v-else
                :src="
                  cert.asset +
                  '#view=FitH&toolbar=0&navpanes=0&scrollbar=0&zoom=page-fit'
                "
                :title="cert.title"
                frameborder="0"
                scrolling="no"
              ></iframe>
            </div>
            <div class="cert-body">
              <span class="badge">{{ cert.year }}</span>
              <h3>{{ cert.title }}</h3>
              <p class="cert-issuer">{{ cert.issuer }}</p>
              <p class="cert-description">{{ cert.description }}</p>
            </div>
          </article>
        </div>
      </div>
    </section>

    <section class="projects" id="projects">
      <div class="section-heading">
        <p class="eyebrow small">Community engagement / Selected work</p>
        <h2>Impact Highlights</h2>
        <p>
          Education, inclusion, and sustainable development in practice. A closer
          look at the projects and community initiatives I have contributed to.
        </p>
      </div>
      <div class="project-gallery">
          <article
            v-for="(project, index) in projects"
            :key="project.title + index"
            class="project-card"
          >
            <div class="media">
              <img :src="project.image" :alt="project.title" loading="lazy" decoding="async" width="640" height="480" />
            </div>
            <div class="project-body">
              <span class="project-number">{{ String(index + 1).padStart(2, '0') }} / Field notes</span>
              <h3>{{ project.title }}</h3>
              <p>{{ project.description }}</p>
              <div class="tags">
                <span v-for="tag in project.tags" :key="tag">{{ tag }}</span>
              </div>
            </div>
          </article>
      </div>
    </section>

    <section class="callout" id="contact">
      <div class="callout-content">
        <p class="eyebrow small">A conversation can be a starting point</p>
        <h2>
          Ready for strategies that build peace, support communities, and
          promote sustainable development?
        </h2>
        <p>
          Have a vision that touches communities or sparks global dialogue?
          Reach out—I’m always excited to connect with people building a more
          peaceful and inclusive world.
        </p>
        <div class="contact-grid">
          <article v-for="link in socialLinks" :key="link.label">
            <p class="label">{{ link.label }}</p>
            <a v-if="link.href" :href="link.href" :target="link.label === 'LinkedIn' ? '_blank' : undefined" rel="noopener noreferrer">{{
              link.value
            }}</a>
            <span v-else>{{ link.value }}</span>
          </article>
        </div>
        <div class="hero-cta">
          <a class="primary" href="mailto:Ikpaochanya60@gmail.com"
            >Start a conversation <span aria-hidden="true">↗</span></a
          >
          <a class="ghost" :href="socialLinks[2].href" target="_blank" rel="noopener noreferrer">Connect on LinkedIn <span aria-hidden="true">↗</span></a>
        </div>
      </div>
    </section>
    </main>

    <footer>
      <p>
        © {{ new Date().getFullYear() }} Ikpa Ochanya Euginia — Crafted with
        intention.
      </p>
      <a href="#home">Back to top ↑</a>
    </footer>
  </div>
</template>
