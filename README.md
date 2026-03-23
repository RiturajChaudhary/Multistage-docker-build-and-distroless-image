# ⚡ Node.js Docker Apps

> **Single-stage vs Multi-stage + Distroless** — shrink your image by ~90% for production.

---

## 📁 Project Structure

```
node-docker-app/
│
├── 📦 node-app/                   # Single-stage Node.js app
│   ├── app.js
│   ├── package.json
│   └── Dockerfile
│
└── 🚀 multi-stage-node-app/       # Multi-stage + distroless Node.js app
    ├── app.js
    ├── package.json
    └── Dockerfile
```

---

## 🚀 Running the Apps

### 📦 Single-Stage Node.js App

```bash
# Build
docker build -t rituraj4164/node-app ./node-app

# Run
docker run -p 8000:8000 rituraj4164/node-app
```

🌐 Open: [http://localhost:8000](http://localhost:8000)

---

### ⚡ Multi-Stage + Distroless Node.js App

```bash
# Build
docker build -t rituraj4164/multi-stage-node-app ./multi-stage-node-app

# Run
docker run -p 3000:3000 rituraj4164/multi-stage-node-app
```

🌐 Open: [http://localhost:3000](http://localhost:3000)

---

## 📊 Image Size Comparison

| Image | Size | Type | Notes |
|:------|-----:|:----:|:------|
| `rituraj4164/node-app:latest` | **1.58 GB** | Single-stage | Full Node.js + npm + bash included |
| `rituraj4164/multi-stage-node-app:latest` | **174 MB** | Multi-stage | Runtime only — no npm, no shell |

> 🏆 **Result: ~90% reduction in image size** — faster pulls, leaner containers, smaller attack surface.

---

## 🔍 How It Works

```
┌─────────────────────────────────────────────────────────┐
│                  SINGLE-STAGE BUILD                     │
│                                                         │
│   FROM node:18                                          │
│   ├── Node.js runtime        ~300 MB                   │
│   ├── npm + package manager  ~100 MB                   │
│   ├── bash + OS tools        ~200 MB                   │
│   ├── build dependencies     ~500 MB                   │
│   └── your app               ~10 MB                    │
│                                         TOTAL: ~1.58 GB │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│               MULTI-STAGE + DISTROLESS                  │
│                                                         │
│   Stage 1 — Builder (thrown away after build)          │
│   ├── Installs all dependencies                        │
│   └── Compiles / prepares the app                      │
│                           ↓ only copies built app       │
│   Stage 2 — Distroless Runtime                         │
│   ├── Node.js runtime only   ~160 MB                   │
│   └── your app               ~10 MB                    │
│                               TOTAL: ~174 MB ✅         │
└─────────────────────────────────────────────────────────┘
```

---

## 🛠️ Dockerfile — Multi-Stage Example

```dockerfile
# ── Stage 1: Builder ──────────────────────────────────────
FROM node:18 AS builder

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

# ── Stage 2: Distroless Runtime ───────────────────────────
FROM gcr.io/distroless/nodejs18-debian12

WORKDIR /app
COPY --from=builder /app /app

CMD ["app.js"]
```

> The builder stage installs everything needed to build the app.
> The final stage gets **only the compiled output** — no npm, no bash, no compilers.

---

## ✅ Optimization Tips

- 🔀 **Always use multi-stage builds** to separate build-time and runtime dependencies.
- 🔒 **Use distroless images** in production — no shell means a dramatically reduced attack surface.
- 🧹 **Avoid unnecessary OS packages** in the final stage — if the app doesn't need it at runtime, don't include it.

---

## 📌 Summary

| Approach | Image Size | Security | Recommended For |
|:---------|:----------:|:--------:|:----------------|
| Single-stage | ~1.58 GB | ⚠️ Lower | Local development |
| Multi-stage + Distroless | ~174 MB | ✅ Higher | **Production** |

**Always prefer multi-stage builds for real deployments.**

---

<div align="center">
  <sub>Built with 🐳 Docker · Node.js 18 · Distroless</sub>
</div>
