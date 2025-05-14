import React, { useEffect, useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { BrowserRouter as Router, Route, Routes, Link } from "react-router-dom";
import { initializeApp } from "firebase/app";
import { getAuth, onAuthStateChanged, signInWithEmailAndPassword, signOut } from "firebase/auth";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);

function useAuth() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (firebaseUser) => {
      setUser(firebaseUser);
    });
    return () => unsubscribe();
  }, []);

  return user;
}

function LoginPage() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const handleLogin = async (e) => {
    e.preventDefault();
    try {
      await signInWithEmailAndPassword(auth, email, password);
      alert("Logged in successfully");
    } catch (err) {
      alert("Login failed: " + err.message);
    }
  };

  return (
    <div className="p-4 md:p-10 max-w-sm mx-auto space-y-6">
      <h2 className="text-2xl font-bold">Login</h2>
      <form onSubmit={handleLogin} className="space-y-4">
        <input
          type="email"
          placeholder="Email"
          className="w-full border p-2 rounded"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
        <input
          type="password"
          placeholder="Password"
          className="w-full border p-2 rounded"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          required
        />
        <Button type="submit" className="bg-red-600 text-white w-full">Log In</Button>
      </form>
    </div>
  );
}

function CommunityPage() {
  const user = useAuth();
  const isHost = user?.email === "LapineMartialArts@gmail.com";

  if (!user) {
    return (
      <div className="p-4 md:p-10 max-w-4xl mx-auto">
        <p>Please <Link to="/login" className="text-blue-600 underline">log in</Link> to access the community page.</p>
      </div>
    );
  }

  return (
    <div className="p-4 md:p-10 max-w-4xl mx-auto space-y-6">
      <h2 className="text-2xl font-bold">Community Page</h2>
      <p className="text-gray-700">Welcome, {user.email}</p>
      <Button onClick={() => signOut(auth)} className="bg-gray-300 text-black">Log Out</Button>

      <div className="mt-6">
        <h3 className="text-xl font-semibold">Submit Photos or Videos for Review</h3>
        <form className="space-y-4">
          <input type="text" placeholder="Your Name" className="w-full border p-2 rounded" required />
          <input type="email" placeholder="Your Email" className="w-full border p-2 rounded" required />
          <input type="file" className="w-full" required />
          <Button type="submit" className="bg-red-600 text-white">Send for Review</Button>
        </form>
      </div>

      {isHost && (
        <div className="mt-6">
          <h3 className="text-xl font-semibold">Teacher Video Uploads</h3>
          <form className="space-y-4">
            <input type="text" placeholder="Video Title" className="w-full border p-2 rounded" required />
            <input type="file" accept="video/*" className="w-full" required />
            <Button type="submit" className="bg-red-600 text-white">Upload Video</Button>
          </form>
        </div>
      )}
    </div>
  );
}

// (Keep other components as-is)

export default function App() {
  return (
    <Router>
      <div className="flex flex-col min-h-screen">
        <div className="flex-grow">
          <Routes>
            <Route path="/" element={<HomePage />} />
            <Route path="/about" element={<AboutPage />} />
            <Route path="/schedule" element={<SchedulePage />} />
            <Route path="/contact" element={<ContactPage />} />
            <Route path="/community" element={<CommunityPage />} />
            <Route path="/login" element={<LoginPage />} />
          </Routes>
        </div>
        <footer className="bg-gray-100 text-center text-sm text-gray-600 py-4 border-t">
          <div>Contact us at: <a href="mailto:LapineMartialArts@gmail.com" className="text-blue-600">LapineMartialArts@gmail.com</a></div>
          <div>1234 My Home, La Pine, Oregon 97739</div>
          <div>Phone: 541.444.8836</div>
        </footer>
      </div>
    </Router>
  );
}
