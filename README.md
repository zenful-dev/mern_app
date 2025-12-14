## Project Folder Structure

```
mern-auth-app/
├── backend/
│   ├── src/
│   │   ├── models/
│   │   │   └── User.js
│   │   ├── routes/
│   │   │   └── auth.routes.js
│   │   ├── app.js
│   │   └── server.js
│   ├── .env
│   ├── package.json
│   └── .gitignore
│
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── components/
│   │       └── Login.jsx
│   ├── index.html
│   ├── package.json
│   └── .gitignore
│
├── .gitignore
└── README.md
```

## 2️⃣ Backend Setup (Express + MongoDB)
#### 📌 Step 1: Create backend folder & initialize npm
```
mkdir backend
cd backend
npm init -y
```

#### 📌 Step 2: Install backend dependencies
```
npm install express mongoose cors dotenv bcryptjs
npm install nodemon --save-dev
```

#### 📌 Step 3: Backend .gitignore
```
backend/.gitignore
node_modules
.env
```

#### 📌 Step 4: Express App Setup
```
src/app.js
const express = require("express");
const cors = require("cors");

const authRoutes = require("./routes/auth.routes");

const app = express();

app.use(cors());
app.use(express.json());

app.use("/api/auth", authRoutes);

module.exports = app;

src/server.js
const mongoose = require("mongoose");
const dotenv = require("dotenv");
const app = require("./app");

dotenv.config();

mongoose
  .connect(process.env.MONGO_URI)
  .then(() => {
    console.log("MongoDB connected");
    app.listen(5000, () => {
      console.log("Server running on port 5000");
    });
  })
  .catch(err => console.error(err));
```

#### 📌 Step 5: User Model

`src/models/User.js`
```
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema(
  {
    email: {
      type: String,
      required: true,
      unique: true,
    },
    password: {
      type: String,
      required: true,
    },
  },
  { timestamps: true }
);

module.exports = mongoose.model("User", userSchema);
```

#### 📌 Step 6: Auth Route (Register API)
`src/routes/auth.routes.js`

```
const express = require("express");
const bcrypt = require("bcryptjs");
const User = require("../models/User");

const router = express.Router();

router.post("/register", async (req, res) => {
  try {
    const { email, password } = req.body;

    if (!email || !password) {
      return res.status(400).json({ message: "All fields required" });
    }

    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(409).json({ message: "User already exists" });
    }

    const hashedPassword = await bcrypt.hash(password, 10);

    await User.create({
      email,
      password: hashedPassword,
    });

    res.status(201).json({ message: "User registered successfully" });
  } catch (error) {
    res.status(500).json({ message: "Server error" });
  }
});

module.exports = router;
```

#### 📌 Step 7: .env
```
MONGO_URI=mongodb://127.0.0.1:27017/mern-auth
```

#### 📌 Step 8: Start Backend
```
npx nodemon src/server.js
```

## 3️⃣ Frontend Setup (React + Vite)

#### 📌 Step 1: Create frontend with Vite

From root folder:
```
npm create vite@latest frontend
cd frontend
npm install
npm run dev
```

Choose:
- Framework → React
- Variant → JavaScript

#### 📌 Step 2: Frontend .gitignore

`frontend/.gitignore`
```
node_modules
dist
```

#### 📌 Step 3: Login Component
`src/components/Login.jsx`
```
import { useState } from "react";

const Login = () => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [message, setMessage] = useState("");

  const handleSubmit = async (e) => {
    e.preventDefault();

    const res = await fetch("http://localhost:5000/api/auth/register", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ email, password }),
    });

    const data = await res.json();
    setMessage(data.message);
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Register</h2>

      <input
        type="email"
        placeholder="Email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />

      <input
        type="password"
        placeholder="Password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
      />

      <button type="submit">Submit</button>

      {message && <p>{message}</p>}
    </form>
  );
};

export default Login;
```
---
`src/App.jsx`
```
import Login from "./components/Login";

function App() {
  return <Login />;
}

export default App;
```

## 4️⃣ Initialize GIT & .gitignore

#### 📌 Step 1: Initialize Git

From root folder:
```
git init
```

#### 📌 Step 2: Root .gitignore
```
node_modules
.env
dist
```

#### 📌 Step 3: First Commit
```
git add .
git commit -m "Initial MERN auth app"
```

## 5️⃣ Push Project to GitHub

#### 📌 Step 1: Create GitHub Repository
- Go to GitHub → New Repository
- Name: mern-auth-app
- Don’t add README (already local)

#### 📌 Step 2: Connect Local Repo to GitHub
```
git branch -M main
git remote add origin https://github.com/USERNAME/mern-auth-app.git
```

#### 📌 Step 3: Push Code
```
git push -u origin main
```