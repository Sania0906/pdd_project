# 🚀 CognifyAI - Complete VS Code Setup Guide

## 📋 Overview

This is a complete full-stack application with:
- **Frontend**: React + TypeScript + Tailwind CSS (50 screens)
- **Backend**: Node.js + Express + PostgreSQL
- **Database**: PostgreSQL with full schema

## 🎯 Quick Start (5 Steps)

### 1️⃣ Setup PostgreSQL Database

```bash
# Install PostgreSQL (choose your OS)

# Windows (with Chocolatey)
choco install postgresql

# Mac (with Homebrew)
brew install postgresql
brew services start postgresql

# Linux (Ubuntu/Debian)
sudo apt-get install postgresql
sudo systemctl start postgresql
```

```bash
# Create database
psql -U postgres
CREATE DATABASE cognifyai;
\q

# Run schema and seed data
psql -U postgres -d cognifyai -f backend/database/schema.sql
psql -U postgres -d cognifyai -f backend/database/seed.sql
```

### 2️⃣ Setup Backend

```bash
# Create backend folder structure
mkdir -p backend/routes backend/middleware backend/database

# Copy all files from BACKEND_CODE.md to respective folders

# Install dependencies
cd backend
npm install

# Create .env file
cp .env.example .env

# Edit .env with your database password
# DB_PASSWORD=your_postgres_password

# Start backend server
npm run dev
```

Backend runs on: **http://localhost:5000** ✅

### 3️⃣ Setup Frontend

```bash
# Navigate to frontend
cd ../frontend

# Install axios for API calls
npm install axios

# Create API service files (see FRONTEND_API_INTEGRATION.md)
mkdir -p src/services

# Create .env file
echo "VITE_API_URL=http://localhost:5000/api" > .env

# Start frontend
npm run dev
```

Frontend runs on: **http://localhost:3000** ✅

### 4️⃣ Test the Connection

```bash
# Test backend health
curl http://localhost:5000/api/health

# Should return: {"status":"OK","message":"CognifyAI API is running"}
```

### 5️⃣ Use the App!

1. Open http://localhost:3000
2. Go through onboarding screens
3. Register a new account
4. Login and explore all 50 screens!

## 📁 Final Folder Structure

```
cognifyai/
├── frontend/                    # React app (current Figma Make project)
│   ├── src/
│   │   ├── app/
│   │   │   ├── components/     # All 50 screens
│   │   │   └── App.tsx
│   │   ├── services/           # API integration
│   │   │   ├── api.ts
│   │   │   ├── authService.ts
│   │   │   ├── skillsService.ts
│   │   │   ├── coursesService.ts
│   │   │   └── jobsService.ts
│   │   └── styles/
│   ├── .env
│   └── package.json
│
├── backend/                     # Node.js + Express API
│   ├── routes/
│   │   ├── auth.js             # Authentication endpoints
│   │   ├── skills.js           # Skills endpoints
│   │   ├── courses.js          # Courses endpoints
│   │   ├── jobs.js             # Jobs endpoints
│   │   ├── users.js            # User endpoints
│   │   ├── careers.js          # Career endpoints
│   │   ├── assessments.js      # Assessment endpoints
│   │   └── analytics.js        # Analytics endpoints
│   ├── middleware/
│   │   └── auth.js             # JWT authentication
│   ├── database/
│   │   ├── schema.sql          # Database schema
│   │   └── seed.sql            # Sample data
│   ├── server.js               # Express server
│   ├── .env                    # Environment variables
│   ├── .env.example            # Environment template
│   └── package.json
│
└── README.md                    # This file
```

## 🗄️ Database Tables (20 tables)

1. **users** - User accounts
2. **skills** - All available skills
3. **user_skills** - User skill proficiencies
4. **courses** - Course catalog
5. **course_enrollments** - User enrollments
6. **course_lessons** - Course content
7. **lesson_progress** - Learning progress
8. **assignments** - Course assignments
9. **assignment_submissions** - User submissions
10. **certificates** - Earned certificates
11. **jobs** - Job listings
12. **saved_jobs** - Bookmarked jobs
13. **job_applications** - Applied jobs
14. **career_paths** - Career information
15. **assessments** - Skill quizzes
16. **assessment_questions** - Quiz questions
17. **assessment_results** - Quiz scores
18. **notifications** - User notifications
19. **user_settings** - User preferences
20. **skill_timeline** - Progress tracking

## 🔌 API Endpoints

### Authentication
- `POST /api/auth/register` - Create account
- `POST /api/auth/login` - Login
- `POST /api/auth/forgot-password` - Password reset

### Skills  
- `GET /api/skills` - All skills
- `GET /api/skills/trending` - Trending skills
- `GET /api/skills/user/:userId` - User's skills 🔒
- `POST /api/skills/user` - Add skill 🔒

### Courses
- `GET /api/courses` - All courses
- `GET /api/courses/:id` - Course details
- `POST /api/courses/enroll` - Enroll 🔒
- `GET /api/courses/my-courses/:userId` - My courses 🔒

### Jobs
- `GET /api/jobs` - All jobs
- `GET /api/jobs/:id` - Job details
- `POST /api/jobs/save` - Save job 🔒
- `POST /api/jobs/apply` - Apply 🔒
- `GET /api/jobs/saved/:userId` - Saved jobs 🔒
- `GET /api/jobs/applied/:userId` - Applications 🔒

🔒 = Requires authentication (JWT token)

## 🧪 Testing the API

### Using VS Code Thunder Client Extension

1. Install Thunder Client extension
2. Create new request
3. Test endpoints:

```http
### Register
POST http://localhost:5000/api/auth/register
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123"
}

### Login
POST http://localhost:5000/api/auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "password123"
}

### Get Skills (copy token from login response)
GET http://localhost:5000/api/skills
Authorization: Bearer YOUR_JWT_TOKEN_HERE
```

## 📱 Frontend Screens (50 Total)

### Onboarding & Auth (11)
1. Splash Screen
2-5. Onboarding (4 screens)
6-11. Login, Signup, Forgot/Reset Password, Email/Phone Verification

### Main App (4)
12. Dashboard
13. Skills Overview
14. Career Overview
15. Profile

### Skills (8)
16-23. Skill Details, Assessment, Categories, Timeline, Trending, Add New, Quiz, Results

### Learning (7)
24-30. Course Catalog, My Courses, Details, Player, Progress, Assignments, Certificates

### Career (8)
31-38. Job Search, Job Details, Saved Jobs, Applied Jobs, Salary Insights, Interview Prep, Career Path, Company Profile

### Settings (9)
39-47. Edit Profile, Account, Notifications, Security, Privacy, Language, Theme, Connected Accounts, Subscription

### Diagrams (3)
48-50. System Architecture, Workflow, Database Flow

## 🛠️ Development Tools

### Recommended VS Code Extensions
- ESLint
- Prettier
- Thunder Client (API testing)
- PostgreSQL Explorer
- GitLens
- Tailwind CSS IntelliSense

### Scripts

**Backend:**
```bash
npm run dev     # Development with auto-reload
npm start       # Production
```

**Frontend:**
```bash
npm run dev     # Development server
npm run build   # Production build
npm run preview # Preview production build
```

## 🐛 Troubleshooting

### Database connection error
```bash
# Check PostgreSQL status
sudo systemctl status postgresql

# Restart PostgreSQL
sudo systemctl restart postgresql

# Verify credentials in .env
```

### Port already in use
```bash
# Find and kill process on port 5000
lsof -ti:5000 | xargs kill -9

# Or use different port in .env
PORT=5001
```

### CORS error
- Verify `FRONTEND_URL` in backend `.env` matches frontend URL
- Check CORS configuration in `server.js`

### Token invalid/expired
- Clear localStorage in browser DevTools
- Login again to get new token

## 🌐 Environment Variables

**Backend (.env):**
```env
PORT=5000
NODE_ENV=development
DB_HOST=localhost
DB_PORT=5432
DB_NAME=cognifyai
DB_USER=postgres
DB_PASSWORD=your_password
JWT_SECRET=your_secret_key
JWT_EXPIRE=7d
FRONTEND_URL=http://localhost:3000
```

**Frontend (.env):**
```env
VITE_API_URL=http://localhost:5000/api
```

## 📚 Documentation Files

1. **BACKEND_SETUP_GUIDE.md** - Detailed backend setup
2. **BACKEND_CODE.md** - All backend code to copy
3. **FRONTEND_API_INTEGRATION.md** - Frontend API integration
4. **README_VSCODE_SETUP.md** - This file (quick start)

## 🎉 You're All Set!

Your complete full-stack CognifyAI application is ready:

✅ 50 beautiful UI screens  
✅ Complete REST API  
✅ PostgreSQL database with sample data  
✅ JWT authentication  
✅ Real-time data integration  

**Happy coding!** 🚀

---

Need help? Check the documentation files or raise an issue!