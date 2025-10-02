# Barabási–Albert (BA) Network Simulator

## Building and Running a Barabási–Albert (BA) Network Simulator

This guide is a comprehensive reference on how to set up, run, and extend a **Barabási–Albert preferential attachment network simulator**. It consolidates everything into one place so that if you (or anyone else) ever gets stuck, you can simply revisit this log.

***

### 🔹 What Is the Barabási–Albert (BA) Model?

The Barabási–Albert model is a **scale-free network model** that generates graphs using a process called _preferential attachment_. This results in networks with a few highly connected hubs and many low-degree nodes, mimicking real-world networks such as:

* The internet
* Power grids
* Social networks
* Airline routes

Understanding and simulating BA networks is useful for studying:

* **Vulnerability to attacks** (removal of hubs)
* **Information spreading** (rumors, viruses, malware)
* **Resilience under random failures**
* **Emergent properties of complex systems**

***

### 🔹 Two Ways to Run the Simulator

You can run the simulator either directly in a **web browser** (simplest), or package it as a **Node.js standalone app** with Express. Both options are covered here.

***

### 1. Running in a Browser (Static Mode)

#### Steps

1. Save your code as `index.html`.
2. Open it in **Chrome, Firefox, or Safari**.
3. Done — it runs fully offline in the browser.

This mode is ideal for:

* Quick experimentation
* Sharing via GitHub Pages, Netlify, or static hosting

***

### 2. Running as a Node.js Standalone App (Dynamic Mode)

#### 📂 Folder Structure

```
ba-simulator/
 ├── app.js
 ├── public/
 │    └── index.html
```

#### app.js (Express Server)

```js
// Minimal Node.js + Express server for BA Simulator
const express = require('express');
const path = require('path');
const app = express();
const PORT = process.env.PORT || 3000;

// Serve static files (HTML, CSS, JS)
app.use(express.static(path.join(__dirname, 'public')));

// Default route
app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

app.listen(PORT, () => {
  console.log(`🚀 Barabási–Albert simulator running at http://localhost:${PORT}`);
});
```

#### Steps to Run

```bash
# Install dependencies
npm init -y
npm install express

# Start server
node app.js
```

Now visit: [http://localhost:3000](http://localhost:3000/)

***

### 3. Making It Truly Standalone

* **Offline**: Works by double-clicking `index.html`.
* **Online**: Works by running `node app.js`.
* **Deployable**: You can host it easily on:
  * GitHub Pages (static browser mode)
  * Netlify or Vercel (static hosting)
  * Heroku or Render (Node.js mode)

***

### 4. Optional Enhancements

#### (a) Add an npm script for convenience

```json
"scripts": {
  "start": "node app.js"
}
```

Then run:

```bash
npm start
```

#### (b) Package as a Desktop App with Electron

If you want a **clickable app** for Windows, macOS, or Linux:

1.  Install Electron:

    ```bash
    npm install electron --save-dev
    ```
2.  Add to `package.json`:

    ```json
    "main": "app.js",
    "scripts": {
      "start": "electron ."
    }
    ```
3.  Run:

    ```bash
    npm start
    ```

This wraps your BA simulator into a full desktop GUI application.

***

### 5. Recommended Frontend Enhancements

From the provided software stack references, you can upgrade the visualization and interactivity:

* **Tailwind CSS** → clean, modern UI styling
* **Chart.js** → degree distribution histograms, infection curves
* **D3.js or vis.js** → animated interactive network visualization
* **jQuery / core-js** → cross-browser support and dynamic UI controls
* **Squarespace / Netlify / Vercel hosting** → quick web deployment

***

### 6. Practical Use Cases for Electrical Engineers

* **Power Grids**: Identify critical substations (hubs) vulnerable to targeted attacks.
* **Communication Networks**: Model malware/worm propagation in SCADA/telemetry systems.
* **Microgrids**: Study synchronization stability with hub-based vulnerabilities.
* **Sensor Networks**: Optimize monitoring placement using network centrality measures.

***

### ✅ Final Notes

* Use **browser-only mode** if you just want a simple, portable simulation.
* Use **Node.js mode** if you want to integrate backend logic or expose simulation APIs.
* Use **Electron packaging** if you want a fully standalone desktop app.

This log serves as a complete reference: from BA network theory → coding → running → packaging → real-world applications.
