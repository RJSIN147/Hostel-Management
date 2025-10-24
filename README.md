# Hostel Management Application

Created using MERN Stack with Redux

## Quick Start

### Local Development

```bash
# Install dependencies
npm install
cd frontend && npm install

# Configure environment variables
cp .env.example .env
# Edit .env with your MongoDB URI and JWT secret

# Start development server
npm run dev
```

### Production Deployment

See [DEPLOYMENT.md](./DEPLOYMENT.md) for detailed deployment instructions on Render.

## Environment Variables

Create a `.env` file in the root directory:

```env
NODE_ENV=development
PORT=5000
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/hostel-management
JWT_SECRET=your-super-secret-jwt-key
REACT_APP_API_URL=http://localhost:5000
```

## Features

- [x] Register/Login Screens
- [x] Student Details
- [x] Add Student
- [x] Edit/Delete Student Details
- [x] Update Student Where abouts
- [x] Can take Attendance Daily
- [x] Dispaly Attendance Details
- [x] Download Csv file of attendance
- [x] Delete Attendance of previous n days
- [x] Control User List
- [x] Edit User Admin Status
- [x] Data Controlled by only Admins
