# Vue Resume Builder Plan

## Goal

Build a simple resume builder with Vue 3 that lets users enter resume details, preview the result live, save it in the browser, and print it as a PDF.

## MVP scope

- Vue 3 with Vite
- JavaScript and plain CSS
- One resume per browser
- One clean resume template
- Form mode and preview mode
- Browser-only `localStorage` persistence
- Native print-to-PDF support
- Responsive layout

## Resume data

```js
{
  personal: {
    name: "",
    title: "",
    email: "",
    phone: "",
    location: "",
    summary: ""
  },
  experience: [],
  education: [],
  skills: [],
  projects: []
}
```

## Components

```text
src/
  App.vue
  components/
    ResumeForm.vue
    PersonalForm.vue
    ExperienceForm.vue
    EducationForm.vue
    SkillsForm.vue
    ResumePreview.vue
```

## Implementation phases

### 1. Initialize the app

- Create the Vue/Vite project.
- Confirm the development server and production build work.

### 2. Add resume state

- Store the resume object in `App.vue`.
- Pass data to form and preview components with Vue props/events.
- Add default empty values.

### 3. Build the form

- Add personal information fields.
- Add repeatable work experience entries.
- Add repeatable education entries.
- Add skills and projects.
- Add remove actions for repeatable entries.

### 4. Build preview mode

- Add a clear switch between edit mode and preview mode.
- Display the formatted resume in preview mode.
- Keep the preview updated as the user edits the form.
- Hide empty sections in the preview.
- Use semantic HTML for headings, lists, and contact details.

### 5. Add persistence and actions

- Save changes only to browser `localStorage`.
- Restore the locally saved resume when the app opens.
- Do not add a backend, account, or remote data storage.
- Add a clear-resume action with confirmation.
- Add a print button using `window.print()`.

### 6. Add styling

- Use a two-column desktop layout.
- Stack the form and preview on small screens.
- Add print styles for an A4-friendly resume.
- Keep controls out of the printed document.

### 7. Verify the MVP

- Test empty fields and empty sections.
- Test adding and removing entries.
- Reload the page and confirm saved data returns.
- Test mobile layout.
- Test print preview and PDF output.
- Run the production build.

## Deferred features

Do not build these until the MVP is useful:

- User accounts
- Backend, database, or cloud storage
- Multiple templates
- Drag-and-drop editing
- AI-generated content
- Sharing links
- Payments

## Definition of done

A user can open the app, enter their resume information, switch between edit and preview modes, reload without losing locally saved data, and print the preview to PDF.
