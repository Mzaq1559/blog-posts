# 📚 My Learning Diary — Posts

This repo contains all the content for my personal
blog [My Learning Diary](your-link-here): written posts, project logs, and the
data behind the site's interactive **Roadmap** section.

## 🗂️ Structure

- `posts/` — each post lives in its own folder with:
  - `README.md` — the actual post content
  - `images/` — all images used in the post
- `roadmap_nodes/` — powers the site's **Roadmap** section (this used to be
  called "Posts" in the site nav). Each topic is a small JSON node with
  `id`, `label`, `category`, `status`, `description`, an `x`/`y` position, and
  `children` (links to dependent/next topics), grouped into category folders:
  - `ai-ml/`
  - `dsa/`
  - `systems/`
  - `web/`
- `project-log/` — write-ups on things I've built.
- `drafts/` — posts in progress, not yet published.

## 📁 Categories

### 🤖 AI & Machine Learning
- API for AI
- Computer Vision
- Generative AI Models
- GPU
- GPUs and TPUs vs CPUs for AI Training
- LLMs
- Loss Functions in ML
- ML Automation Pipelines
- ML Evaluation Metrics
- MLOps
- Neurons to ChatGPT — Neural Networks & LLMs
- Overfitting
- Reinforcement Learning
- Tensor Cores vs CUDA Cores

### 🌐 Web Development
- Cookies vs Local Storage
- CSR vs SSR
- From JavaScript to TypeScript — A Complete Guide
- How Browsers Render HTML
- JavaScript Event Loop
- JavaScript Frameworks
  - ⚛️ React — A Practical Intermediate Guide
  - 🅰️ Angular — A Practical Intermediate Guide
  - 💚 Vue.js — A Practical Intermediate Guide
  - ▲ Next.js — A Practical Intermediate Guide
- JWT Authentication
- Modern JavaScript Features
- NPM and Yarn
- Progressive Web Apps

### 🔐 Networking & Security
- HTTPS
- IPv6
- Penetration Testing Tools
- QUIC
- Symmetric vs Asymmetric Encryption
- TCP vs UDP
- The Dark Web
- The OSI Model
- VPN

### 🗄️ Databases
- Understanding Databases

### ☁️ Cloud & DevOps
- Evolution of Cloud Computing
- Kubernetes

### 🔧 DevOps & Tools
- Branching and Merging
- Build Tools
- Git and GitHub Workflow

### 💻 Systems & OS
- IP Routing
- Linux Startup Sequence
- Managing Services

### ⚡ Tech
- FastAPI Backend from Scratch

### 🚀 Project Log
- Building a Git-Based CMS in 1 Week
- How to Run SQL Server in Docker and Connect 
  it with Azure Data Studio
- SQL Server + Docker + Azure Data Studio 
  + Northwind Database
- I Leaked My GitHub Token — and Fixed It
- Deploying SiteFlowAI to Azure
- Redoing My Portfolio: Real Stats, Real Projects
- Learning FastAPI by Building an Issue Tracker
- job-application-mcp: Building the Tools, Then Hitting the First Deploy Blockers
- job-application-mcp: OAuth, Azure, and the Claude Web Connection
- From MIT to Source-Available: Licensing job-application-mcp for v1.0.0
- Building DocVision AI: A Full CV Pipeline, Pair-Programmed in One Sitting
- Go Assistant: An Android Overlay That Watches a Go Board and Talks to Claude Vision

## 🗺️ Roadmap section

The site's old **Posts** nav item is now **Roadmap** — an interactive,
category-organized map of everything I'm learning or plan to learn, with
each node showing a status (e.g. planned, in-progress, done) and links to
the topics that build on it. The data for it lives in `roadmap_nodes/`,
split by category as described above.

## 🚀 About

I am a forth semester student doing bachelors in computer science i am planning to document everything i learn in post-log along with all the other posts in blog-post, feel free to add a post if you want.... 

## 📝 Latest Posts

implenting backpropagation from scratch by following the tutorial on micrograd. i will soon create a blog post for it too
