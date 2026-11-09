# Week 11: Multi-Page Portfolio with React Router & Advanced Hooks
## Navigate Your Professional Journey with Dynamic Routing

### 🎯 Learning Objectives
By completing this project, you will:
- Set up React Router v6 with `BrowserRouter`, `Routes` and `Route`
- Turn one page into five with client-side navigation (`Link`, `NavLink` with an active style)
- Read a URL parameter with `useParams` and navigate in code with `useNavigate`
- Write a custom `useFetch` hook with loading and error states and `AbortController` cleanup
- Use `useRef` to focus an input without re-rendering
- Write a custom `useLocalStorage` hook and use it for the theme
- Stretch (Tiers): a 404 route, nested routes with `Outlet`, code splitting with `lazy` and `Suspense`

### 📚 Prerequisites
Before starting, you should have:
- Attended the Week 11 lecture (React Router and advanced hooks)
- Read React Front To Back, chapters 5 (React Router) and 12 (Advanced Hooks)
- Node 18 or newer (`node -v`)
- Your week 9 portfolio open in another window for your own text (optional; nothing is copied from it today)

### 🏗️ Project Overview
Your portfolio becomes a real multi-page app: a home page, a projects grid, one page per project read from the URL, about and contact, all without a full page reload. Then the data comes through a hook you wrote, the search box focuses itself, and the theme survives a refresh.

**What the starter gives you:** a complete Vite + React app whose pieces are already built: five pages under `src/pages/` (`HomePage`, `ProjectsPage`, `ProjectDetailPage`, `AboutPage`, `ContactPage`), `Navigation`, `ProjectCard` and `SearchBar` under `src/components/`, six sample projects in `src/data/projects.js` (and the same six as `public/projects.json`, which Part 4 fetches), two hook stubs under `src/hooks/`, and all the CSS (`.nav-link.active`, `.spinner`, `.error`, `.theme-dark` are already styled). `react-router-dom` is already in `package.json`. The app starts and shows a placeholder with a TODO list; every Part below turns one `// TODO` comment in the starter into working code.

**Fill the TODO in place; never create a file that exists or paste a second copy of a line.** Every code block below replaces the lines next to a `TODO` comment in a file that already exists. If a block repeats a line the starter already has (an `import`, a `const`), keep the starter's line once. A duplicated `import` or `const` is a syntax error that blanks the whole page.

### 🚀 Getting Started

**Today's core is Parts 1 to 4 (about 50 minutes after a 10-minute setup). Parts 5 and 6 are the stretch, the Tiers come after. Each Part ends with a Checkpoint that says what you should see.**

#### First: Create your own copy of this template
This repository is a GitHub **template** (GitHub Classroom is not used this term). Make your own copy of it, then clone that copy.

1. On this repository's page, click the green **Use this template** button and choose **Create a new repository**. On the next screen: Owner = your own GitHub account, Repository name = `in-person-project-9-<your-github-username>`, visibility **Public**. Click **Create repository**.
2. **Clone your new repository:**
   ```bash
   git clone https://github.com/<your-github-username>/in-person-project-9-<your-github-username>.git
   cd in-person-project-9-<your-github-username>
   ```

#### Second: Install and run (start this now)
Today there is one server, Vite. Start the install first; on classroom wifi it can take a few minutes, and you can read Part 1 while it runs.

1. **Check Node:** `node -v` must print `v18` or newer. If it prints an older version or "command not found", install the current LTS from https://nodejs.org, open a new terminal and check again.
2. **Install dependencies** (once per clone; `react-router-dom` is already listed, there is nothing extra to install):
   ```bash
   npm install
   ```
3. **Start the dev server** and keep it running all session:
   ```bash
   npm run dev
   ```
4. **Check:** open http://localhost:5173. You should see "Welcome to Your Portfolio" and a TODO box. The nav links at the top are plain `<a>` tags for now: clicking them reloads the page and shows the same placeholder. That is expected until Part 1.

**Bringing your week 9 content?** This is a fresh repository; nothing continues from your IPP 7 repo. After class, copy your own text (name, skills, project titles) into the starter's pages and into `src/data/projects.js` and `public/projects.json`. Keep the starter's structure: the file names, routes and class names in this README are the starter's.

### 📝 Step-by-Step Instructions

#### Part 1: Routes (15 minutes)

1. **Wrap the app with `BrowserRouter`** in `src/main.jsx`. Replace the `TODO` import comment with the import, and the `TODO` wrap comment plus `<App />` with the wrapped app:
```jsx
import { BrowserRouter } from 'react-router-dom'
```
```jsx
    <BrowserRouter>
      <App />
    </BrowserRouter>
```
   `src/main.jsx` now has one `import` for the router and `<App />` sits inside `<BrowserRouter>` inside `<React.StrictMode>`.

2. **Import the router pieces and the five pages** in `src/App.jsx`. Replace the two `TODO` import comments at the top (keep the `useState` and `Navigation` imports the file already has):
```jsx
import { Routes, Route } from 'react-router-dom'
import HomePage from './pages/HomePage'
import ProjectsPage from './pages/ProjectsPage'
import ProjectDetailPage from './pages/ProjectDetailPage'
import AboutPage from './pages/AboutPage'
import ContactPage from './pages/ContactPage'
```

3. **Replace the placeholder with routes.** In `src/App.jsx`, inside `<main className="app-main">`, delete the `TODO` comment and the whole `<div className="placeholder"> ... </div>` block (through its closing `</div>`, about 15 lines) and put this in its place. Keep the `<header>` and `<footer>` as they are:
```jsx
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/projects" element={<ProjectsPage />} />
          <Route path="/projects/:id" element={<ProjectDetailPage />} />
          <Route path="/about" element={<AboutPage />} />
          <Route path="/contact" element={<ContactPage />} />
        </Routes>
```

**Checkpoint 1:** the placeholder is gone and the home page ("Good morning! I'm a Full Stack Developer") shows. Type http://localhost:5173/projects, then `/about`, then `/contact` into the address bar: each shows its page. `/projects/task-manager` shows the Task Manager project (the detail page still shows the first project whatever the id; Part 3 fixes that). The nav links still reload the page; Part 2.

What's happening here:
- `BrowserRouter` listens to the browser's URL and history
- `Routes` picks the ONE `Route` whose `path` matches, and renders its `element`
- `:id` is a URL parameter; Part 3 reads it with `useParams`

#### Part 2: Links (10 minutes)

1. **Navigation with an active style.** In `src/components/Navigation.jsx`, replace the `TODO` import comment with:
```jsx
import { NavLink } from 'react-router-dom'
```
   and replace the four `<a href=... className="nav-link">` elements (and the `TODO` comment above them) with:
```jsx
      <NavLink to="/" className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}>
        Home
      </NavLink>
      <NavLink to="/projects" className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}>
        Projects
      </NavLink>
      <NavLink to="/about" className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}>
        About
      </NavLink>
      <NavLink to="/contact" className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}>
        Contact
      </NavLink>
```
   No CSS to write: `.nav-link.active` is already styled in `src/styles/App.css` (line 48).

2. **Home page buttons become links.** In `src/pages/HomePage.jsx`, replace the `TODO` import comment with:
```jsx
import { Link } from 'react-router-dom'
```
   and replace the `TODO: Replace these buttons` comment and the two `<button>` elements inside `.hero-buttons` with:
```jsx
          <Link to="/projects" className="btn btn-primary">
            View My Projects
          </Link>
          <Link to="/contact" className="btn btn-secondary">
            Get In Touch
          </Link>
```

3. **Each project card links to its page.** In `src/pages/ProjectsPage.jsx`, replace the `TODO: Import Link` comment with:
```jsx
import { Link } from 'react-router-dom'
```
   and inside `.projects-grid`, replace the `TODO: Wrap ProjectCard` comment and the `<ProjectCard key=... />` line with:
```jsx
          <Link key={project.id} to={`/projects/${project.id}`} className="project-card-link">
            <ProjectCard project={project} />
          </Link>
```
   (`key` moves to the outer element; `ProjectCard` has no `<a>` of its own, so the link is valid HTML.)

**Checkpoint 2:** clicking Projects in the nav changes the page with no reload (the tab's favicon does not flicker) and the Projects link is highlighted with the underline. Home's two buttons go to Projects and Contact. Clicking a project card opens `/projects/<its id>`. The browser Back button works.

What's happening here:
- `Link` and `NavLink` change the URL through the router instead of asking the server for a new page
- `NavLink` passes `{ isActive }` to a `className` function, so the active link styles itself

#### Part 3: One page per project (15 minutes)

1. **Read the id from the URL.** In `src/pages/ProjectDetailPage.jsx`, replace the `TODO` import comment at the top with:
```jsx
import { useParams, useNavigate } from 'react-router-dom'
```
   Then inside the function, replace the three `TODO` blocks above `if (!project)` (the `useParams` comment, the `useNavigate` comment, and the "Find the project" comment with its `const project = projects[0]` line) with:
```jsx
  const { id } = useParams()
  const navigate = useNavigate()
  const project = projects.find(p => p.id === id)
```

2. **Not found gets a way back.** The `if (!project)` block already handles a missing project (delete the `TODO: Handle case` comment above it). Inside the block, replace the `TODO: Add button` comment with:
```jsx
        <button className="btn btn-primary" onClick={() => navigate('/projects')}>
          Back to Projects
        </button>
```

3. **A back button on the real page.** At the top of `<div className="project-detail-page">`, replace the `TODO: Add a back button` comment with:
```jsx
      <button className="btn btn-secondary" onClick={() => navigate(-1)}>
        ← Back
      </button>
```

4. **Contact form sends you home.** In `src/pages/ContactPage.jsx`, replace the `TODO` import comment with:
```jsx
import { useNavigate } from 'react-router-dom'
```
   add one line as the first line inside `function ContactPage() {`:
```jsx
  const navigate = useNavigate()
```
   and inside `handleSubmit`'s `setTimeout`, replace the `TODO: Navigate to home page` comment and the commented `setTimeout` line below it with:
```jsx
      setTimeout(() => navigate('/'), 2000)
```

**Checkpoint 3:** `/projects/weather-dashboard` shows the Weather Dashboard (title, 2025, category, features). `/projects/nope` shows "Project not found" with a working Back to Projects button. The ← Back button returns to wherever you came from. Submitting the contact form shows the thank-you line, then lands on Home two seconds later.

What's happening here:
- `useParams()` returns the URL parameters as an object: `{ id: 'weather-dashboard' }`
- `useNavigate()` returns a function; `navigate('/projects')` goes to a path, `navigate(-1)` goes back one step in history

#### Part 4: A `useFetch` hook, and data that is fetched (10 minutes)

The projects grid still reads `projects` from `src/data/projects.js`. The same six projects are served as a file at http://localhost:5173/projects.json (it is `public/projects.json`; Vite serves everything in `public/` at the site root). Part 4 fetches that file through your own hook.

1. **Write the hook.** In `src/hooks/useFetch.js`, replace the whole `export function useFetch(url) { ... }` block (the one with the TODO comments and the mock return) with this. Delete the commented "Example implementation structure" at the bottom of the file too:
```jsx
export function useFetch(url) {
  const [data, setData] = useState(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)

  useEffect(() => {
    const abortController = new AbortController()

    const fetchData = async () => {
      try {
        setLoading(true)
        setError(null)
        const response = await fetch(url, { signal: abortController.signal })
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`)
        }
        const result = await response.json()
        setData(result)
      } catch (err) {
        if (err.name !== 'AbortError') {
          setError(err.message)
        }
      } finally {
        setLoading(false)
      }
    }

    fetchData()

    return () => abortController.abort()
  }, [url])

  return { data, loading, error }
}
```
   The `import { useState, useEffect } from 'react'` at the top of the file stays as it is.

2. **Use it on the Projects page.** In `src/pages/ProjectsPage.jsx`:
   - replace the `TODO: Import the custom useFetch hook` comment with `import { useFetch } from '../hooks/useFetch'`
   - delete the line `import { projects } from '../data/projects'`
   - replace the first line inside the function (`const [filteredProjects, setFilteredProjects] = useState(projects)`) and the `TODO: Replace this with the useFetch` block below it with:
```jsx
  const { data: projects, loading, error } = useFetch('/projects.json')
  const [searchTerm, setSearchTerm] = useState('')
```
   - replace the whole `handleSearch` function with:
```jsx
  const handleSearch = (term) => setSearchTerm(term)

  const filteredProjects = (projects || []).filter(project =>
    project.title.toLowerCase().includes(searchTerm.toLowerCase()) ||
    project.description.toLowerCase().includes(searchTerm.toLowerCase()) ||
    project.technologies.some(tech =>
      tech.toLowerCase().includes(searchTerm.toLowerCase())
    )
  )
```
   - replace the `TODO: Add loading and error states` comment (below `<SearchBar />`) with:
```jsx
      {loading && <div className="spinner">Loading projects...</div>}
      {error && <div className="error">Could not load projects: {error}</div>}
```

**Checkpoint 4:** the Projects page shows "Loading projects..." for an instant, then the six cards; the search box still filters as you type. In DevTools, Network tab, you see one request for `projects.json`. Change the URL in `useFetch('/projects.json')` to `/nope.json` and the page shows the red error box; change it back.

What's happening here:
- A custom hook is a function that uses hooks; it packages state and an effect behind one call
- `[url]` re-runs the fetch when the URL changes; the cleanup aborts a fetch that is still in flight when the page unmounts
- `projects || []` keeps the filter safe on the first render, when `data` is still `null`

#### Part 5 (stretch): `useRef` for search focus (10 minutes)

The search box is a controlled input (`value` and `onChange` stay). `useRef` adds focus management without touching the state.

In `src/components/SearchBar.jsx`:
1. Replace the `TODO: Import useRef` comment and change the first line to import both hooks: `import { useState, useRef, useEffect } from 'react'` (one import line, not two).
2. Replace the `TODO: Create a ref` comment and its commented line with:
```jsx
  const searchInputRef = useRef(null)
```
3. Replace the `TODO: Add useEffect to auto-focus` comment with:
```jsx
  useEffect(() => {
    searchInputRef.current?.focus()
  }, [])
```
4. In `handleClear`, replace the `TODO: Focus the input after clearing` comment with:
```jsx
    searchInputRef.current?.focus()
```
5. On the `<input>`, replace the `TODO: Add ref attribute` comment with:
```jsx
          ref={searchInputRef}
```

**Checkpoint 5:** open /projects: the cursor is already in the search box. Type something, click ✕: the box empties, all six cards return, and the cursor is back in the box.

#### Part 6 (stretch): `useLocalStorage`, and a theme that survives a refresh (10 minutes)

1. **Write the hook.** In `src/hooks/useLocalStorage.js`, replace the whole `export function useLocalStorage(key, initialValue) { ... }` block and the commented example below it with:
```jsx
export function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key)
      return item ? JSON.parse(item) : initialValue
    } catch (error) {
      console.error(`Error loading ${key} from localStorage:`, error)
      return initialValue
    }
  })

  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value
      setStoredValue(valueToStore)
      window.localStorage.setItem(key, JSON.stringify(valueToStore))
    } catch (error) {
      console.error(`Error saving ${key} to localStorage:`, error)
    }
  }

  return [storedValue, setValue]
}
```
   The file's first line still imports `useEffect`, which this hook does not use; change it to `import { useState } from 'react'`.

2. **Use it for the theme.** In `src/App.jsx`:
   - add `import { useLocalStorage } from './hooks/useLocalStorage'` under the page imports
   - replace `const [theme, setTheme] = useState('light')` (and the comment above it) with `const [theme, setTheme] = useLocalStorage('portfolio-theme', 'light')`
   - the file no longer uses `useState`: change its first line to remove that import (an unused import is only a lint warning, but leave the file clean)

**Checkpoint 6:** click the moon, the theme goes dark; refresh the page, it stays dark. DevTools, Application tab, Local Storage: a `portfolio-theme` key with `"dark"`.

### 🏆 Challenge Extensions (if you finish early)

**Tier 1 (about 10 minutes): a 404 page.** Add a `NotFoundPage` under `src/pages/` (an `<h1>` and a `Link` home) and a last route `<Route path="*" element={<NotFoundPage />} />`.
- **Done when:** http://localhost:5173/anything shows your 404 page with a working link home, and every other route still works.

**Tier 2 (about 15 minutes, after Tier 1): nested routes with a shared layout.** Create `src/pages/ProjectsLayout.jsx` that renders a heading, then `<Outlet />`. Nest the projects routes under it: `<Route path="/projects" element={<ProjectsLayout />}>` with `<Route index element={<ProjectsPage />} />` and `<Route path=":id" element={<ProjectDetailPage />} />` inside.
- **Done when:** the heading stays on screen while you move between the grid and a project page, and both URLs still work when typed directly.

**Tier 3 (253A, about 20 minutes, a bounded task): code splitting.** Load the five pages with `lazy(() => import('./pages/HomePage'))` and wrap `<Routes>` in `<Suspense fallback={<div className="spinner">Loading...</div>}>`.
- **Done when:** `npm run build` prints one JavaScript chunk per page (six `.js` files under `dist/assets/` instead of one), and the app still navigates everywhere with no errors in the console.

### 🐛 Troubleshooting Guide

**The page is blank and the console says "Cannot read properties of undefined (reading 'pathname')" or "useRoutes() may be used only in the context of a <Router>".** `<App />` is not inside `<BrowserRouter>` in `src/main.jsx` (Part 1, step 1).

**"Identifier 'Link' has already been declared" (or any duplicate import or const).** A code block was pasted next to a line the starter already had. Keep one copy.

**Nav links reload the whole page.** They are still `<a>` tags; Part 2 replaces them with `NavLink`. (Links to other sites, like the GitHub button on a project page, are meant to stay `<a>`.)

**The project page always shows Task Manager.** `const project = projects[0]` is still there; Part 3 step 1 replaces it with `projects.find(...)`.

**Projects page shows "Could not load projects: HTTP error! status: 404".** The URL in `useFetch(...)` must be `/projects.json` with the leading slash, and `public/projects.json` must exist.

**Search filters nothing after Part 4.** `handleSearch` must set `searchTerm`, and `filteredProjects` must be computed from `projects || []`, as in Part 4 step 2.

**Refreshing on `/projects` gives a 404 on a deployed site.** Only on static hosts: they must serve `index.html` for every path (Netlify: a `_redirects` file with `/* /index.html 200`). The Vite dev server already does this.

### 📖 Resources & References
- [React Router v6 documentation](https://reactrouter.com/en/main)
- [useRef](https://react.dev/reference/react/useRef)
- [Reusing logic with custom hooks](https://react.dev/learn/reusing-logic-with-custom-hooks)
- [lazy and Suspense](https://react.dev/reference/react/lazy)

### ✅ What "complete" means (complete/incomplete grade)

A pushed repository with commits made today whose app shows, in this order:
- (a) Part 1: five routes that render when typed into the address bar
- (b) Part 2: nav links that change the page without a reload, the active one highlighted, and project cards that open `/projects/<id>`
- (c) Part 3: `/projects/weather-dashboard` shows that project; `/projects/nope` shows Not found with a working button
- (d) Part 4: the Projects page loads its six projects through `useFetch('/projects.json')` with a visible loading state

Parts 5 and 6 and the Tiers are optional. If you stop early, push what works: an app with (a) to (c) and a note in the commit message is worth more than a broken Part 4.

Before you leave: `git add .`, `git commit -m "In-person project 9"`, `git push`, then paste your repository URL into the bCourses assignment.
