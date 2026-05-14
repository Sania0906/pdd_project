# Frontend API Integration Guide

## Step 1: Install Axios

```bash
cd frontend
npm install axios
```

## Step 2: Create API Service

Create `src/services/api.ts`:

```typescript
import axios from 'axios';

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:5000/api';

const api = axios.create({
  baseURL: API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Add token to requests
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Handle errors
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Token expired or invalid
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

## Step 3: Create API Functions

Create `src/services/authService.ts`:

```typescript
import api from './api';

export interface LoginData {
  email: string;
  password: string;
}

export interface RegisterData {
  name: string;
  email: string;
  password: string;
}

export const authService = {
  async register(data: RegisterData) {
    const response = await api.post('/auth/register', data);
    if (response.data.success) {
      localStorage.setItem('token', response.data.data.token);
    }
    return response.data;
  },

  async login(data: LoginData) {
    const response = await api.post('/auth/login', data);
    if (response.data.success) {
      localStorage.setItem('token', response.data.data.token);
    }
    return response.data;
  },

  logout() {
    localStorage.removeItem('token');
  },

  getToken() {
    return localStorage.getItem('token');
  },

  isAuthenticated() {
    return !!this.getToken();
  },
};
```

Create `src/services/skillsService.ts`:

```typescript
import api from './api';

export const skillsService = {
  async getAllSkills() {
    const response = await api.get('/skills');
    return response.data;
  },

  async getTrendingSkills() {
    const response = await api.get('/skills/trending');
    return response.data;
  },

  async getUserSkills(userId: number) {
    const response = await api.get(`/skills/user/${userId}`);
    return response.data;
  },

  async addSkillToUser(userId: number, skillId: number, proficiencyLevel: number, yearsOfExperience: number) {
    const response = await api.post('/skills/user', {
      userId,
      skillId,
      proficiencyLevel,
      yearsOfExperience,
    });
    return response.data;
  },

  async getSkillTimeline(userId: number) {
    const response = await api.get(`/skills/timeline/${userId}`);
    return response.data;
  },
};
```

Create `src/services/coursesService.ts`:

```typescript
import api from './api';

export const coursesService = {
  async getAllCourses() {
    const response = await api.get('/courses');
    return response.data;
  },

  async getCourseById(id: number) {
    const response = await api.get(`/courses/${id}`);
    return response.data;
  },

  async enrollInCourse(userId: number, courseId: number) {
    const response = await api.post('/courses/enroll', { userId, courseId });
    return response.data;
  },

  async getUserCourses(userId: number) {
    const response = await api.get(`/courses/my-courses/${userId}`);
    return response.data;
  },
};
```

Create `src/services/jobsService.ts`:

```typescript
import api from './api';

export const jobsService = {
  async getAllJobs() {
    const response = await api.get('/jobs');
    return response.data;
  },

  async getJobById(id: number) {
    const response = await api.get(`/jobs/${id}`);
    return response.data;
  },

  async saveJob(userId: number, jobId: number) {
    const response = await api.post('/jobs/save', { userId, jobId });
    return response.data;
  },

  async applyForJob(userId: number, jobId: number, coverLetter: string) {
    const response = await api.post('/jobs/apply', {
      userId,
      jobId,
      coverLetter,
    });
    return response.data;
  },

  async getSavedJobs(userId: number) {
    const response = await api.get(`/jobs/saved/${userId}`);
    return response.data;
  },

  async getAppliedJobs(userId: number) {
    const response = await api.get(`/jobs/applied/${userId}`);
    return response.data;
  },
};
```

## Step 4: Update App.tsx

```typescript
import { useState, useEffect } from 'react';
import { authService } from './services/authService';
import { skillsService } from './services/skillsService';

export default function App() {
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  const [userData, setUserData] = useState({
    id: 0,
    name: 'User',
    email: 'user@email.com'
  });

  useEffect(() => {
    const token = authService.getToken();
    if (token) {
      setIsAuthenticated(true);
      // Fetch user data from token or API
    }
  }, []);

  const handleLogin = async (email: string, password: string) => {
    try {
      const response = await authService.login({ email, password });
      setUserData(response.data.user);
      setIsAuthenticated(true);
      setCurrentView('home');
    } catch (error) {
      console.error('Login failed:', error);
      alert('Login failed. Please check your credentials.');
    }
  };

  const handleSignup = async (name: string, email: string, password: string) => {
    try {
      const response = await authService.register({ name, email, password });
      setUserData(response.data.user);
      setIsAuthenticated(true);
      setCurrentView('home');
    } catch (error) {
      console.error('Signup failed:', error);
      alert('Signup failed. Please try again.');
    }
  };

  const handleLogout = () => {
    authService.logout();
    setIsAuthenticated(false);
    setCurrentView('login');
  };

  // Rest of your App component...
}
```

## Step 5: Update LoginScreen.tsx

```typescript
import { useState } from 'react';
import { Brain, Mail, Lock, ArrowRight } from 'lucide-react';

interface LoginScreenProps {
  onLogin: (email: string, password: string) => Promise<void>;
  onNavigateToSignup: () => void;
}

export default function LoginScreen({ onLogin, onNavigateToSignup }: LoginScreenProps) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setLoading(true);
    try {
      await onLogin(email, password);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  };

  return (
    // ... your JSX with updated form handling
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        disabled={loading}
        // ... other props
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        disabled={loading}
        // ... other props
      />
      <button type="submit" disabled={loading}>
        {loading ? 'Signing in...' : 'Sign In'}
      </button>
    </form>
  );
}
```

## Step 6: Update SignupScreen.tsx

```typescript
import { useState } from 'react';

interface SignupScreenProps {
  onSignup: (name: string, email: string, password: string) => Promise<void>;
  onNavigateToLogin: () => void;
}

export default function SignupScreen({ onSignup, onNavigateToLogin }: SignupScreenProps) {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    password: '',
    confirmPassword: ''
  });
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    if (formData.password !== formData.confirmPassword) {
      alert('Passwords do not match!');
      return;
    }
    setLoading(true);
    try {
      await onSignup(formData.name, formData.email, formData.password);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  };

  // ... rest of component
}
```

## Step 7: Fetch Real Data in DashboardScreen.tsx

```typescript
import { useEffect, useState } from 'react';
import { skillsService } from '../services/skillsService';
import { jobsService } from '../services/jobsService';

interface DashboardScreenProps {
  userName: string;
  userId: number;
}

export default function DashboardScreen({ userName, userId }: DashboardScreenProps) {
  const [skills, setSkills] = useState([]);
  const [recommendedJobs, setRecommendedJobs] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const [skillsData, jobsData] = await Promise.all([
          skillsService.getUserSkills(userId),
          jobsService.getAllJobs()
        ]);
        setSkills(skillsData.data);
        setRecommendedJobs(jobsData.data.slice(0, 3));
      } catch (error) {
        console.error('Error fetching dashboard data:', error);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [userId]);

  if (loading) {
    return <div>Loading...</div>;
  }

  // ... rest of component with real data
}
```

## Step 8: Create .env file in frontend

Create `.env` in your frontend root:

```env
VITE_API_URL=http://localhost:5000/api
```

## Step 9: Update package.json proxy (if needed)

In `frontend/package.json`, you can add:

```json
{
  "proxy": "http://localhost:5000"
}
```

## Testing the Integration

1. Start backend: `cd backend && npm run dev`
2. Start frontend: `cd frontend && npm run dev`
3. Open http://localhost:3000
4. Try registering a new user
5. Login with the credentials
6. See real data from the database!

---

**Your frontend is now connected to the backend!** 🎉