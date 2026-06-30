# Hybrid Mixed Reality Collaboration Platform

A WebXR-based collaborative platform for working at your computer — not replacing it, but enhancing it. Two collaborators anywhere in the world can share and simultaneously edit code, 3D models, and interactive widgets, with everything superimposed over their live video feed using optical see-through mixed reality.

Currently running on **Meta Quest 3s via WebXR**, and targeting **Project Aura** as the ideal platform for its unobtrusive eyeglasses form factor.

---

## What It Does

- **Live collaborative coding** — two users share and co-edit code in real time; changes are reflected instantly in the shared 3D scene
- **Freehand drawing in 3D space** — draw curves and shapes in the air with hand tracking; drawings morph into interactive widgets and 3D objects
- **Curve-based animation** — interactive curve editor drives robot joint animations live
- **Simultaneous 3D model interaction** — collaborators see and manipulate the same 3D objects (including articulated robots with movable joints) superimposed over their video
- **Screen-anchored AR** — QR codes on the computer screen are used to 6DOF-locate the screen in physical space; the wearable then stays locked to the screen's position and orientation
- **Seamless object migration** — any software object (code, text, diagram, widget, 3D drawing) can migrate between your computer screen and the surrounding physical space
- **Unobtrusive form factor** — designed for optical see-through glasses so two collaborators can work together in a coffee shop, in-person or remotely, without drawing attention

## Why Project Aura

Our use case is best served by optical see-through devices. A head-mounted display that occludes reality (like an Apple Vision Pro or Meta Quest 3) compromises the user's view of their computer screen and their surroundings, and is socially conspicuous. Project Aura's eyeglasses form factor is the natural target for this application.

---

## Tech Stack

- **WebXR** with custom GLSL shaders
- **WebRTC** (via [bici](#bici-video-conferencing)) for low-latency peer-to-peer state sync between clients
- **Socket.io** for server-side coordination
- **Node.js / Express** backend
- **JavaScript** custom interactive widgets that work in both 2D screen and 3D XR contexts
- Hand tracking via WebXR joint tracking API
- QR-code-based 6DOF screen localization

The freehand drawing → interactive widget morphing system is adapted from the [Chalktalk](https://github.com/kenperlin/chalktalk) system (Perlin et al., arXiv:1809.07166).

---

## Repository Structure

```
/                   Main XR platform (WebXR app, scenes, shaders, server)
  js/scenes/        Scene definitions — robot, IK, drawing, multiplayer, etc.
  shaders/          Custom GLSL shaders
  server/           Node.js signaling + API server
  bici/             Lightweight video conferencing component (see below)
```

---

## bici — Video Conferencing

The `bici/` folder contains **bici**, a lightweight platform for enhanced video chat, originally implemented by Ken Perlin in 2025.

> In Spanish, *bici* means "bicycle" — a lightweight and efficient means of transport.  
> In Chinese, *彼此 (bǐcǐ)* means "each other."

Bici provides peer-to-peer WebRTC video chat with private 1-on-1 rooms, synchronized code editing, and shared pen strokes — the video conferencing layer that enables remote mixed reality collaboration.

### Bici Features

- Private 1-on-1 rooms with unique room codes and shareable invite links
- Peer-to-peer video/audio (direct WebRTC, low latency)
- Room-scoped collaborative code editor (Yjs-backed, syncs only within your room)
- Synchronized pen strokes between room members
- Video/audio/visibility toggle controls
- Minimal UI — small video thumbnails in the bottom-right corner

### Running bici

```sh
cd bici
npm install
npm start
# Open http://localhost:8000
# Press 'h' for the help menu
```

See [bici/WEBRTC_SETUP.md](bici/WEBRTC_SETUP.md) for full setup, deployment, and troubleshooting details.

---

## Setup

Install Node.js and npm. This project was tested with **Node v18.20.8**; if you run into issues, switch to this version.

```sh
npm install
cd server
npm install
source patch
```

If `source patch` does not work:

```sh
sh patch_fixed.sh
```

---

## Running Locally

1. From the root folder: `./startserver`
2. Open `chrome://flags/` in Google Chrome
3. Search **"Insecure origins treated as secure"** and enable it
4. Add `http://[your-computer-ip]:2026` to the text box (e.g. `http://10.19.127.1:2026`)
5. Relaunch Chrome and go to `http://localhost:2026`

---

## Running in VR (Meta Quest 3)

1. Run the server locally (see above)
2. Open the browser on your Quest headset
3. Go to `chrome://flags/`
4. Enable **"Insecure origins treated as secure"** and add `http://[your-computer-ip]:2026`
5. Relaunch the browser and navigate to `http://[your-computer-ip]:2026`

---

## Debugging in VR

1. In the Oculus app on your phone, go to **Devices** → select your headset → enable **Developer Mode**
2. Connect the Quest to your computer via USB
3. On your computer, open `chrome://inspect#devices`
4. Accept **Allow USB Debugging** on the headset when prompted
5. Your device will appear under **Remote Target** — click **Inspect** on the XR window

---

## Creating Your Own Scene

1. Create a `.js` file in [js/scenes/](js/scenes/) using [shapes.js](js/scenes/shapes.js) as a template
2. Register it in [js/scenes/scenes.js](js/scenes/scenes.js) by adding the name and path to the `scenes` export
3. Hot-reloading is enabled (`enableSceneReloading = true`) — save changes and see them live

---

## Enabling Hand Tracking

In the Quest browser, go to `chrome://flags/` and enable:

- `#webxr-hands` — WebXR experiences with joint tracking
- `#webxr-depth-sorting` — WebXR Layers depth sorting
- `#webxr-layers` — WebXR Layers
- `#webxr-phase-sync` — phase sync support

Then in **Quest Settings → Device → Hands and Controllers**, enable **Auto Enable Hands or Controllers**, and enter the XR experience.

---

## References

- Perlin et al. *Chalktalk: A Visualization and Communication Language* — arXiv:1809.07166  
  https://github.com/kenperlin/chalktalk
