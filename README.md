## Hi there 👋

<!--
**SaeedBoomAI/SaeedBoomAI** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
SaeedBoomAI/
├── package.json
├── pages/
│   └── index.js
├── styles/
│   └── globals.css
└── README.md
{
  "name": "saeedboomai",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
  "dependencies": {
    "next": "14.2.3",
    "react": "18.3.1",
    "react-dom": "18.3.1",
    "axios": "^1.6.7"
  }
}
import { useState } from "react";
import axios from "axios";

export default function Home() {
  const [prompt, setPrompt] = useState("");
  const [videoUrl, setVideoUrl] = useState("");
  const [loading, setLoading] = useState(false);

  const generateVideo = async () => {
    if (!prompt) return alert("Please enter a prompt!");
    setLoading(true);
    setVideoUrl("");

    try {
      const res = await axios.post("/api/generate", { prompt });
      setVideoUrl(res.data.videoUrl);
    } catch (err) {
      alert("Error generating video 😢");
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-600 to-indigo-800 flex flex-col items-center justify-center p-6 text-white">
      <h1 className="text-4xl font-bold mb-6">🎬 SaeedBoomAI</h1>
      <p className="mb-4 text-lg text-center">
        Generate Emotional Storytelling AI Shorts from your prompt.
      </p>
      <input
        type="text"
        placeholder="Enter your story prompt..."
        value={prompt}
        onChange={(e) => setPrompt(e.target.value)}
        className="w-full max-w-md p-3 rounded-lg text-black"
      />
      <button
        onClick={generateVideo}
        disabled={loading}
        className="mt-4 px-6 py-3 bg-yellow-400 text-black rounded-lg font-semibold"
      >
        {loading ? "Generating..." : "Generate Video"}
      </button>

      {videoUrl && (
        <div className="mt-6">
          <video src={videoUrl} controls className="rounded-lg shadow-lg w-80" />
          <a
            href={videoUrl}
            download
            className="block text-center mt-2 underline text-yellow-300"
          >
            Download Video
          </a>
        </div>
      )}
    </div>
  );
}
import axios from "axios";

export default async function handler(req, res) {
  if (req.method !== "POST") return res.status(405).end();
  const { prompt } = req.body;

  try {
    const HF_API_TOKEN = process.env.HF_API_KEY; // তোমার Hugging Face API key
    const response = await axios.post(
      "https://api-inference.huggingface.co/models/damo-vilab/text-to-video-ms-1.7b",
      { inputs: prompt },
      {
        headers: { Authorization: `Bearer ${HF_API_TOKEN}` },
        responseType: "arraybuffer",
      }
    );

    const base64Video = Buffer.from(response.data, "binary").toString("base64");
    const videoUrl = `data:video/mp4;base64,${base64Video}`;

    res.status(200).json({ videoUrl });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
}
body {
  margin: 0;
  font-family: 'Inter', sans-serif;
}
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
};

module.exports = nextConfig;
pages/api
generate.js
import axios from "axios";

export default async function handler(req, res) {
  if (req.method !== "POST") return res.status(405).end();
  const { prompt } = req.body;

  try {
    const HF_API_TOKEN = process.env.HF_API_KEY; // তোমার Hugging Face API key
    const response = await axios.post(
      "https://api-inference.huggingface.co/models/damo-vilab/text-to-video-ms-1.7b",
      { inputs: prompt },
      {
        headers: { Authorization: `Bearer ${HF_API_TOKEN}` },
        responseType: "arraybuffer",
      }
    );

    const base64Video = Buffer.from(response.data, "binary").toString("base64");
    const videoUrl = `data:video/mp4;base64,${base64Video}`;

    res.status(200).json({ videoUrl });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
}
