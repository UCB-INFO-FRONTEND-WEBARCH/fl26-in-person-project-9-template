# Week 11: Multi-Page Portfolio with React Router & Advanced Hooks
## Navigate Your Professional Journey with Dynamic Routing

### 🎯 Learning Objectives
By completing this project, you will:
- Set up React Router v6 with BrowserRouter, Routes, and Route components
- Create multiple portfolio pages with client-side navigation
- Implement dynamic routes using useParams for project detail pages
- Build nested routes with shared layouts using Outlet
- Use useRef for DOM manipulation and input focus management
- Create reusable custom hooks (useFetch and useLocalStorage)
- Optimize performance with useMemo and useCallback

### 📚 Prerequisites
Before starting, you should have:
- Completed Week 11 lecture on React Router & Advanced Hooks
- Reviewed prep work: React Front To Back Chapters 5 (React Router) & 12 (Advanced Hooks)
- Your portfolio from Week 9 or 10 (optional but recommended)
- Understanding of React state, effects, and Context API

### 🏗️ Project Overview
Transform your single-page React portfolio into a full multi-page application with proper routing, dynamic project pages, and advanced React patterns. You'll implement client-side routing to create distinct pages for your portfolio sections, add dynamic routes for individual project details, and enhance the user experience with custom hooks for data management and performance optimization.

### 🚀 Getting Started

**First:** Accept the GitHub Classroom assignment link provided by your instructor. This will create a new repository with the Week 11 starter code.

1. **Clone your new repository:**
   ```bash
   git clone [your-github-classroom-repo-url]
   cd [your-repo-name]
   ```

2. **Choose your path:**

#### Option A: Continue from Previous Week (Recommended)
If you completed the Week 9 or 10 portfolio project:

1. **Delete the starter files** in your cloned repository (but keep the .git folder)
2. **Copy your previous week's project files** into the cloned repository:
   ```bash
   # Example: Copy from Week 10
   cp -r ~/path-to-week10-project/src/* ./src/
   cp ~/path-to-week10-project/package.json ./
   cp ~/path-to-week10-project/vite.config.js ./
   ```

3. **Install React Router:**
   ```bash
   npm install react-router-dom
   ```

4. **Install all dependencies and start:**
   ```bash
   npm install
   npm run dev
   ```

#### Option B: Fresh Start
If starting fresh, use the provided starter files:

1. **Use the existing starter files** (already in your cloned repository)
2. **Install dependencies:**
   ```bash
   npm install
   ```
3. **Start the development server:**
   ```bash
   npm run dev
   ```
4. **Open your browser** to `http://localhost:5173`

### 📝 Step-by-Step Instructions

#### Part 1: Setting Up React Router (15 minutes)

1. **Wrap your App with BrowserRouter** in `src/main.jsx`:
```jsx
import { BrowserRouter } from 'react-router-dom'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
)
```

2. **Create your page components** in `src/pages/`:
   - `HomePage.jsx` - Landing page with intro
   - `ProjectsPage.jsx` - Grid of all projects
   - `ProjectDetailPage.jsx` - Individual project details
   - `AboutPage.jsx` - About section
   - `ContactPage.jsx` - Contact information

3. **Set up routes** in `src/App.jsx`:
```jsx
import { Routes, Route } from 'react-router-dom'

function App() {
  return (
    <div className="app">
      <Navigation />
      <Routes>
        <Route path="/" element={<HomePage />} />
        <Route path="/projects" element={<ProjectsPage />} />
        <Route path="/projects/:id" element={<ProjectDetailPage />} />
        <Route path="/about" element={<AboutPage />} />
        <Route path="/contact" element={<ContactPage />} />
      </Routes>
    </div>
  )
}
```

What's happening here:
- BrowserRouter enables HTML5 history API for clean URLs
- Routes container holds all route definitions
- Each Route maps a URL path to a component
- `:id` creates a dynamic route parameter

Common mistakes to avoid:
- Don't use regular `<a>` tags - they cause full page reloads
- Remember to wrap everything in BrowserRouter at the root level

#### Part 2: Navigation Component with Active Links (10 minutes)

1. **Create a Navigation component** using NavLink for active styling:
```jsx
import { NavLink } from 'react-router-dom'

function Navigation() {
  return (
    <nav className="navigation">
      <NavLink
        to="/"
        className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}
      >
        Home
      </NavLink>
      <NavLink
        to="/projects"
        className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}
      >
        Projects
      </NavLink>
      <NavLink to="/about" className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}>
        About
      </NavLink>
      <NavLink to="/contact" className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}>
        Contact
      </NavLink>
    </nav>
  )
}
```

2. **Style the active state** in your CSS:
```css
.nav-link {
  color: #666;
  padding: 0.5rem 1rem;
  transition: color 0.3s;
}

.nav-link.active {
  color: #007bff;
  font-weight: bold;
  border-bottom: 2px solid #007bff;
}
```

What's happening here:
- NavLink knows which route is active
- The className function receives isActive as a parameter
- Active links get special styling automatically

#### Part 3: Dynamic Project Routes with useParams (15 minutes)

1. **Create project data** in `src/data/projects.js`:
```js
export const projects = [
  {
    id: 'portfolio-site',
    title: 'Personal Portfolio',
    description: 'A React-based portfolio with routing',
    technologies: ['React', 'React Router', 'CSS'],
    imageUrl: '/images/portfolio.png',
    githubUrl: 'https://github.com/...',
    liveUrl: 'https://...',
    details: 'Full description of the project...'
  },
  // Add more projects...
]
```

2. **Implement ProjectDetailPage** with useParams:
```jsx
import { useParams, useNavigate } from 'react-router-dom'
import { projects } from '../data/projects'

function ProjectDetailPage() {
  const { id } = useParams()
  const navigate = useNavigate()
  const project = projects.find(p => p.id === id)

  if (!project) {
    return (
      <div className="error-page">
        <h2>Project not found</h2>
        <button onClick={() => navigate('/projects')}>
          Back to Projects
        </button>
      </div>
    )
  }

  return (
    <div className="project-detail">
      <button onClick={() => navigate(-1)}>← Back</button>
      <h1>{project.title}</h1>
      <img src={project.imageUrl} alt={project.title} />
      <p>{project.details}</p>
      <div className="tech-stack">
        {project.technologies.map(tech => (
          <span key={tech} className="tech-badge">{tech}</span>
        ))}
      </div>
    </div>
  )
}
```

What's happening here:
- useParams extracts the :id from the URL
- useNavigate provides programmatic navigation
- navigate(-1) goes back one page in history

#### Part 4: Creating a Custom useFetch Hook (10 minutes)

1. **Create the useFetch hook** in `src/hooks/useFetch.js`:
```jsx
import { useState, useEffect } from 'react'

export function useFetch(url) {
  const [data, setData] = useState(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)

  useEffect(() => {
    const abortController = new AbortController()

    const fetchData = async () => {
      try {
        setLoading(true)
        const response = await fetch(url, {
          signal: abortController.signal
        })

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

2. **Use the custom hook** in your components:
```jsx
function ProjectsPage() {
  const { data: projects, loading, error } = useFetch('/api/projects')

  if (loading) return <div className="spinner">Loading...</div>
  if (error) return <div className="error">Error: {error}</div>

  return (
    <div className="projects-grid">
      {projects?.map(project => (
        <ProjectCard key={project.id} project={project} />
      ))}
    </div>
  )
}
```

What's happening here:
- Custom hook encapsulates fetch logic
- AbortController prevents memory leaks
- Returns loading, error, and data states

#### Part 5: useRef for Search Focus (10 minutes)

1. **Create a search component** with auto-focus:
```jsx
import { useRef, useEffect } from 'react'

function SearchBar({ onSearch }) {
  const searchInputRef = useRef(null)

  useEffect(() => {
    // Auto-focus on mount
    searchInputRef.current?.focus()
  }, [])

  const handleKeyPress = (e) => {
    if (e.key === 'Enter') {
      onSearch(searchInputRef.current.value)
    }
  }

  const handleClear = () => {
    searchInputRef.current.value = ''
    searchInputRef.current.focus()
  }

  return (
    <div className="search-bar">
      <input
        ref={searchInputRef}
        type="text"
        placeholder="Search projects..."
        onKeyPress={handleKeyPress}
      />
      <button onClick={handleClear}>Clear</button>
    </div>
  )
}
```

What's happening here:
- useRef provides direct DOM access
- .current holds the actual DOM element
- Focus management improves accessibility

#### Part 6: Custom useLocalStorage Hook (10 minutes)

1. **Create the useLocalStorage hook** in `src/hooks/useLocalStorage.js`:
```jsx
import { useState, useEffect } from 'react'

export function useLocalStorage(key, initialValue) {
  // Get from local storage then parse stored json or return initialValue
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key)
      return item ? JSON.parse(item) : initialValue
    } catch (error) {
      console.error(`Error loading ${key} from localStorage:`, error)
      return initialValue
    }
  })

  // Return a wrapped version of useState's setter function that persists the new value to localStorage
  const setValue = (value) => {
    try {
      // Allow value to be a function so we have the same API as useState
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

2. **Use it for theme persistence**:
```jsx
function App() {
  const [theme, setTheme] = useLocalStorage('portfolio-theme', 'light')

  return (
    <div className={`app theme-${theme}`}>
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
      {/* Rest of app */}
    </div>
  )
}
```

### 🎨 Customization Ideas
- Add page transition animations between routes
- Implement a 404 page for unknown routes
- Add breadcrumb navigation for nested routes
- Create a blog section with markdown support
- Add URL query parameters for filtering projects

### 🏆 Challenge Extensions

**Intermediate: Nested Routes with Layout**
Create a shared layout for project pages:
```jsx
<Route path="/projects" element={<ProjectLayout />}>
  <Route index element={<ProjectsGrid />} />
  <Route path=":id" element={<ProjectDetail />} />
</Route>
```

**Advanced: Protected Routes**
Implement an admin section with authentication:
```jsx
function ProtectedRoute({ children }) {
  const { user } = useAuth()
  return user ? children : <Navigate to="/login" />
}
```

**Graduate (253A): Performance Optimization**
Implement code splitting with lazy loading:
```jsx
const ProjectsPage = lazy(() => import('./pages/ProjectsPage'))

// In routes:
<Route
  path="/projects"
  element={
    <Suspense fallback={<Loading />}>
      <ProjectsPage />
    </Suspense>
  }
/>
```

### 🐛 Troubleshooting Guide

**Issue: "Cannot read properties of undefined (reading 'pathname')"**
- Solution: Make sure BrowserRouter wraps your entire App in main.jsx

**Issue: "useRoutes() may be used only in the context of a <Router>"**
- Solution: Check that all routing hooks are inside BrowserRouter

**Issue: Page refreshes lose the route (404 error)**
- Solution: Configure your server to serve index.html for all routes (SPA configuration)

**Issue: Links cause full page reload**
- Solution: Use Link or NavLink from react-router-dom, not regular `<a>` tags

**Issue: Active styling not working with NavLink**
- Solution: Use a function for className that receives `{ isActive }`

### 📖 Resources & References
- [React Router v6 Documentation](https://reactrouter.com/en/main)
- [useRef Hook Guide](https://react.dev/reference/react/useRef)
- [Custom Hooks Best Practices](https://react.dev/learn/reusing-logic-with-custom-hooks)
- [useMemo and useCallback](https://react.dev/reference/react/useMemo)

### ✅ Project Checklist
Before considering your project complete, ensure you have:

- [ ] Set up React Router with at least 5 routes
- [ ] Implemented navigation with active link highlighting
- [ ] Created at least one dynamic route with useParams
- [ ] Built a custom useFetch hook for data fetching
- [ ] Used useRef for at least one DOM interaction
- [ ] Implemented useLocalStorage for persistence
- [ ] Added error handling for invalid routes
- [ ] Tested all navigation paths work correctly
- [ ] Ensured browser back/forward buttons work
- [ ] Verified the app works on refresh

### 🎯 Learning Outcomes
By completing this project, you've gained practical experience with:
- Modern client-side routing patterns
- Dynamic URL parameters and navigation
- Custom hooks for code reusability
- Performance optimization techniques
- Real-world React application architecture

You now have a professional multi-page portfolio that showcases both your projects and your React Router skills!