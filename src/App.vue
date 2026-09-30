<script setup>
import { computed, reactive, ref, watch } from 'vue'

const emptyResume = () => ({
  personal: { name: '', title: '', email: '', phone: '', location: '', linkedin: '', portfolio: '', summary: '' },
  experience: [],
  education: [],
  certifications: [],
  skills: [],
  projects: []
})

const saved = localStorage.getItem('resume-builder')
const stored = saved ? JSON.parse(saved) : {}
const defaults = emptyResume()
const resume = reactive({ ...defaults, ...stored, personal: { ...defaults.personal, ...(stored.personal || {}) } })
const mode = ref('edit')
const savedAt = ref('')

watch(resume, value => {
  localStorage.setItem('resume-builder', JSON.stringify(value))
  savedAt.value = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
}, { deep: true })

const hasContent = computed(() => Object.values(resume.personal).some(Boolean) ||
  resume.experience.length || resume.education.length || resume.certifications.length || resume.skills.length || resume.projects.length)

function add(section) {
  const values = {
    experience: { role: '', company: '', start: '', end: '', description: '' },
    education: { degree: '', school: '', year: '' },
    certifications: { name: '', issuer: '', year: '', link: '' },
    skills: { name: '' },
    projects: { name: '', description: '', link: '' }
  }
  resume[section].push(values[section])
}

function clearResume() {
  if (!confirm('Clear this resume?')) return
  Object.assign(resume, emptyResume())
  localStorage.removeItem('resume-builder')
  savedAt.value = ''
}

function printResume() {
  mode.value = 'preview'
  setTimeout(() => window.print(), 0)
}

function bullets(value) {
  return value.split(/\r?\n/).map(line => line.replace(/^\s*[•*-]\s*/, '').trim()).filter(Boolean)
}
</script>

<template>
  <header class="toolbar">
    <div>
      <h1>Resume Builder</h1>
      <span v-if="savedAt" class="saved">Saved locally at {{ savedAt }}</span>
    </div>
    <nav aria-label="Resume actions">
      <button :class="{ active: mode === 'edit' }" @click="mode = 'edit'">Edit</button>
      <button :class="{ active: mode === 'preview' }" @click="mode = 'preview'">Preview</button>
      <button class="print" @click="printResume">Print PDF</button>
    </nav>
  </header>

  <main :class="['workspace', mode]">
    <section v-if="mode === 'edit'" class="form-panel" aria-label="Resume form">
      <h2>Resume details</h2>
      <p class="hint">Your resume is saved only in this browser.</p>

      <fieldset>
        <legend>Personal information</legend>
        <label>Full name <input v-model="resume.personal.name" placeholder="Jane Doe" /></label>
        <label>Professional title <input v-model="resume.personal.title" placeholder="Frontend Developer" /></label>
        <div class="grid">
          <label>Email <input v-model="resume.personal.email" type="email" placeholder="jane@example.com" /></label>
          <label>Phone <input v-model="resume.personal.phone" placeholder="+1 555 123 4567" /></label>
        </div>
          <label>Location <input v-model="resume.personal.location" placeholder="Manila, Philippines" /></label>
          <div class="grid"><label>LinkedIn <input v-model="resume.personal.linkedin" type="url" placeholder="https://linkedin.com/in/username" /></label><label>Portfolio <input v-model="resume.personal.portfolio" type="url" placeholder="https://yourportfolio.com" /></label></div>
        <label>Summary <textarea v-model="resume.personal.summary" rows="4" placeholder="A short professional introduction..."></textarea></label>
      </fieldset>

      <fieldset>
        <legend>Work experience</legend>
        <article v-for="(item, index) in resume.experience" :key="item" class="entry">
          <div class="entry-title"><strong>Experience {{ index + 1 }}</strong><button class="remove" @click="resume.experience.splice(index, 1)">Remove</button></div>
          <div class="grid"><label>Role <input v-model="item.role" /></label><label>Company <input v-model="item.company" /></label></div>
          <div class="grid"><label>Start <input v-model="item.start" placeholder="2022" /></label><label>End <input v-model="item.end" placeholder="Present" /></label></div>
          <label>Description <textarea v-model="item.description" rows="3"></textarea></label>
        </article>
        <button class="add" @click="add('experience')">+ Add experience</button>
      </fieldset>

      <fieldset>
        <legend>Education</legend>
        <article v-for="(item, index) in resume.education" :key="item" class="entry">
          <div class="entry-title"><strong>Education {{ index + 1 }}</strong><button class="remove" @click="resume.education.splice(index, 1)">Remove</button></div>
          <div class="grid"><label>Degree <input v-model="item.degree" /></label><label>School <input v-model="item.school" /></label></div>
          <label>Year <input v-model="item.year" /></label>
        </article>
        <button class="add" @click="add('education')">+ Add education</button>
      </fieldset>

      <fieldset>
        <legend>Certifications</legend>
        <article v-for="(item, index) in resume.certifications" :key="item" class="entry">
          <div class="entry-title"><strong>Certification {{ index + 1 }}</strong><button class="remove" @click="resume.certifications.splice(index, 1)">Remove</button></div>
          <label>Name <input v-model="item.name" placeholder="Certification name" /></label>
          <div class="grid"><label>Issuer <input v-model="item.issuer" /></label><label>Year <input v-model="item.year" /></label></div>
          <label>Credential link <input v-model="item.link" type="url" placeholder="https://..." /></label>
        </article>
        <button class="add" @click="add('certifications')">+ Add certification</button>
      </fieldset>

      <fieldset>
        <legend>Skills</legend>
        <div v-for="(item, index) in resume.skills" :key="item" class="inline-entry"><input v-model="item.name" placeholder="Vue.js" /><button class="remove" @click="resume.skills.splice(index, 1)">Remove</button></div>
        <button class="add" @click="add('skills')">+ Add skill</button>
      </fieldset>

      <fieldset>
        <legend>Projects</legend>
        <article v-for="(item, index) in resume.projects" :key="item" class="entry">
          <div class="entry-title"><strong>Project {{ index + 1 }}</strong><button class="remove" @click="resume.projects.splice(index, 1)">Remove</button></div>
          <label>Name <input v-model="item.name" /></label>
          <label>Link <input v-model="item.link" placeholder="https://..." /></label>
          <label>Description <textarea v-model="item.description" rows="3"></textarea></label>
        </article>
        <button class="add" @click="add('projects')">+ Add project</button>
      </fieldset>

      <button class="clear" @click="clearResume">Clear resume</button>
    </section>

    <section class="preview-panel" aria-label="Resume preview">
      <div class="paper">
        <template v-if="hasContent">
          <header class="resume-header">
            <h2>{{ resume.personal.name || 'Your Name' }}</h2>
            <p v-if="resume.personal.title">{{ resume.personal.title }}</p>
            <div class="contact"><span>{{ [resume.personal.email, resume.personal.phone, resume.personal.location].filter(Boolean).join(' • ') }}</span><span v-if="resume.personal.linkedin || resume.personal.portfolio"> • </span><a v-if="resume.personal.linkedin" :href="resume.personal.linkedin" target="_blank" rel="noopener noreferrer">LinkedIn</a><span v-if="resume.personal.linkedin && resume.personal.portfolio"> • </span><a v-if="resume.personal.portfolio" :href="resume.personal.portfolio" target="_blank" rel="noopener noreferrer">Portfolio</a></div>
          </header>
          <div class="resume-columns">
            <div>
              <section v-if="resume.personal.summary"><h3>Profile</h3><p>{{ resume.personal.summary }}</p></section>
              <section v-if="resume.experience.length"><h3>Experience</h3><article v-for="item in resume.experience" :key="item"><h4>{{ item.role }} <span v-if="item.company">— {{ item.company }}</span></h4><small>{{ item.start }} <span v-if="item.start || item.end">—</span> {{ item.end }}</small><ul v-if="item.description"><li v-for="line in bullets(item.description)" :key="line">{{ line }}</li></ul></article></section>
            </div>
            <div>
              <section v-if="resume.education.length"><h3>Education</h3><article v-for="item in resume.education" :key="item"><h4>{{ item.degree }}</h4><p>{{ item.school }} <span v-if="item.year">({{ item.year }})</span></p></article></section>
              <section v-if="resume.certifications.length"><h3>Certifications</h3><article v-for="item in resume.certifications" :key="item"><h4>{{ item.name }} <a v-if="item.link" :href="item.link" target="_blank" rel="noopener noreferrer">View credential</a></h4><p>{{ item.issuer }} <span v-if="item.year">({{ item.year }})</span></p></article></section>
              <section v-if="resume.skills.length"><h3>Skills</h3><p>{{ resume.skills.map(item => item.name).filter(Boolean).join(' • ') }}</p></section>
              <section v-if="resume.projects.length" class="projects"><h3>Projects</h3><article v-for="item in resume.projects" :key="item"><h4>{{ item.name }} <a v-if="item.link" :href="item.link" target="_blank" rel="noopener noreferrer">View project</a></h4><p>{{ item.description }}</p></article></section>
            </div>
          </div>
        </template>
        <div v-else class="empty"><h2>Your resume preview</h2><p>Start entering your details to see them here.</p></div>
      </div>
    </section>
  </main>
</template>
