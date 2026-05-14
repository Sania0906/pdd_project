# CognifyAI - Full Stack Setup Guide for VS Code

This guide will help you set up the complete CognifyAI application with Frontend (React), Backend (Node.js/Express), and Database (PostgreSQL) in VS Code.

## 📁 Project Structure

```
cognifyai/
├── frontend/          # React + TypeScript (copy from current Figma Make project)
│   ├── src/
│   ├── public/
│   └── package.json
├── backend/           # Node.js + Express API
│   ├── routes/
│   ├── middleware/
│   ├── database/
│   └── server.js
└── README.md
```

## 🚀 Step 1: Setup PostgreSQL Database

### Install PostgreSQL
**Windows:**
```bash
# Download from: https://www.postgresql.org/download/windows/
# Or use chocolatey:
choco install postgresql
```

**Mac:**
```bash
brew install postgresql
brew services start postgresql
```

**Linux:**
```bash
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
sudo systemctl start postgresql
```

### Create Database
```bash
# Open PostgreSQL terminal
psql -U postgres

# Create database
CREATE DATABASE cognifyai;

# Exit
\q
```

### Run Schema
```bash
psql -U postgres -d cognifyai < backend/database/schema.sql
psql -U postgres -d cognifyai < backend/database/seed.sql
```

## 🛠️ Step 2: Setup Backend

### Navigate to backend folder
```bash
cd backend
```

### Install Dependencies
```bash
npm install
```

### Create .env file
```bash
cp .env.example .env
```

### Edit .env file with your configuration
```env
PORT=5000
NODE_ENV=development

DB_HOST=localhost
DB_PORT=5432
DB_NAME=cognifyai
DB_USER=postgres
DB_PASSWORD=your_actual_password

JWT_SECRET=your_super_secret_jwt_key_change_this
JWT_EXPIRE=7d

FRONTEND_URL=http://localhost:3000
```

### Start Backend Server
```bash
# Development mode with auto-reload
npm run dev

# Production mode
npm start
```

Backend will run on: **http://localhost:5000**

## 💻 Step 3: Setup Frontend

### Navigate to frontend folder
```bash
cd ../frontend
```

### Install Dependencies
```bash
npm install
```

### Create .env file
```env
VITE_API_URL=http://localhost:5000/api
```

### Update API Configuration
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

export default api;
```

### Start Frontend
```bash
npm run dev
```

Frontend will run on: **http://localhost:3000**

## 📝 Step 4: Test the Application

### 1. Register a New User
```
POST http://localhost:5000/api/auth/register
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123"
}
```

### 2. Login
```
POST http://localhost:5000/api/auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "password123"
}
```

### 3. Get Skills
```
GET http://localhost:5000/api/skills
```

## 🔌 API Endpoints

### Authentication
- `POST /api/auth/register` - Register new user
- `POST /api/auth/login` - Login user
- `POST /api/auth/forgot-password` - Request password reset

### Skills
- `GET /api/skills` - Get all skills
- `GET /api/skills/trending` - Get trending skills
- `GET /api/skills/categories` - Get skill categories
- `GET /api/skills/user/:userId` - Get user's skills (Protected)
- `POST /api/skills/user` - Add skill to user (Protected)
- `GET /api/skills/timeline/:userId` - Get skill timeline (Protected)

### Courses
- `GET /api/courses` - Get all courses
- `GET /api/courses/:id` - Get course details
- `POST /api/courses/enroll` - Enroll in course (Protected)
- `GET /api/courses/my-courses/:userId` - Get user's courses (Protected)

### Jobs
- `GET /api/jobs` - Get all jobs
- `GET /api/jobs/:id` - Get job details
- `POST /api/jobs/save` - Save job (Protected)
- `POST /api/jobs/apply` - Apply for job (Protected)
- `GET /api/jobs/saved/:userId` - Get saved jobs (Protected)
- `GET /api/jobs/applied/:userId` - Get applied jobs (Protected)

### Career
- `GET /api/careers/paths` - Get career paths
- `GET /api/careers/recommendations/:userId` - Get AI career recommendations (Protected)
- `GET /api/careers/salary-insights` - Get salary insights

### Assessments
- `GET /api/assessments` - Get all assessments
- `GET /api/assessments/:id` - Get assessment details
- `POST /api/assessments/submit` - Submit assessment (Protected)
- `GET /api/assessments/results/:userId` - Get user results (Protected)

### Analytics
- `GET /api/analytics/dashboard/:userId` - Get user dashboard analytics (Protected)
- `GET /api/analytics/progress/:userId` - Get learning progress (Protected)

## 🧪 Testing with Postman/Thunder Client

1. Install Thunder Client extension in VS Code
2. Import the API endpoints
3. Set up environment variables:
   - `api_url`: http://localhost:5000/api
   - `token`: (Get from login response)

## 📦 Database Tables

The application uses these main tables:
- `users` - User accounts
- `skills` - All available skills
- `user_skills` - User's skill proficiencies
- `courses` - Course catalog
- `course_enrollments` - User course enrollments
- `jobs` - Job listings
- `job_applications` - User job applications
- `assessments` - Skill assessments/quizzes
- `assessment_results` - User assessment scores
- `career_paths` - Career path information
- `certificates` - User certificates
- `notifications` - User notifications

## 🔐 Authentication Flow

1. User registers → Receives JWT token
2. Token stored in localStorage/cookies
3. Token sent in Authorization header for protected routes
4. Backend validates token using JWT middleware

## 🎨 Frontend Integration

Update your React components to use the API:

```typescript
// Example: Login component
import api from '../services/api';

const handleLogin = async (email: string, password: string) => {
  try {
    const response = await api.post('/auth/login', { email, password });
    localStorage.setItem('token', response.data.data.token);
    // Navigate to dashboard
  } catch (error) {
    console.error('Login failed:', error);
  }
};

// Example: Get user skills
const fetchUserSkills = async (userId: number) => {
  try {
    const response = await api.get(`/skills/user/${userId}`);
    setSkills(response.data.data);
  } catch (error) {
    console.error('Failed to fetch skills:', error);
  }
};
```

## 🐛 Troubleshooting

### Database Connection Error
- Verify PostgreSQL is running: `sudo systemctl status postgresql`
- Check .env database credentials
- Ensure database exists: `psql -U postgres -l`

### Port Already in Use
```bash
# Find process using port 5000
lsof -i :5000

# Kill the process
kill -9 <PID>
```

### CORS Issues
- Verify FRONTEND_URL in backend .env matches your frontend URL
- Check CORS configuration in server.js

## 📚 Additional Resources

- [Express.js Documentation](https://expressjs.com/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [JWT Authentication](https://jwt.io/)
- [React with TypeScript](https://react-typescript-cheatsheet.netlify.app/)

## 🎯 Next Steps

1. ✅ Set up database
2. ✅ Configure backend environment
3. ✅ Start backend server
4. ✅ Configure frontend environment
5. ✅ Start frontend development server
6. ✅ Test authentication flow
7. ✅ Integrate frontend with backend API
8. 🚀 Deploy to production!

---

**Your CognifyAI full-stack application is ready!** 🎉