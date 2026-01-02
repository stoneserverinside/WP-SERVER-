const express = require("express");
const fs = require("fs");
const path = require("path");
const pino = require("pino");
const multer = require("multer");

const {
  makeCacheableSignalKeyStore,
  useMultiFileAuthState,
  delay,
  Browsers,
  fetchLatestBaileysVersion,
  makeWASocket
} = require("@whiskeysockets/baileys");

const app = express();

/* ✅ IMPORTANT: Render PORT FIX */
const PORT = process.env.PORT || 21562;

/* ✅ Create folders safely */
const TEMP_DIR = path.join(__dirname, "temp");
const UPLOAD_DIR = path.join(__dirname, "uploads");

if (!fs.existsSync(TEMP_DIR)) fs.mkdirSync(TEMP_DIR, { recursive: true });
if (!fs.existsSync(UPLOAD_DIR)) fs.mkdirSync(UPLOAD_DIR, { recursive: true });

const upload = multer({ dest: UPLOAD_DIR });

app.use(express.json());
app.use(express.urlencoded({ extended: true }));

/* Store sessions & tasks */
const activeClients = new Map();
const activeTasks = new Map();

/* ================= HOME PAGE ================= */
app.get("/", (req, res) => {
  res.send(`
    <h2>WhatsApp Server Running ✅</h2>
    <form action="/code" method="get">
      <input name="number" placeholder="WhatsApp Number with country code" required />
      <button type="submit">Get Pairing Code</button>
    </form>
    <hr>
    <form action="/send-message" method="post" enctype="multipart/form-data">
      <select name="targetType" required>
        <option value="number">Number</option>
        <option value="group">Group</option>
      </select>
      <input name="target" placeholder="Target number / group id" required />
      <input type="file" name="messageFile" accept=".txt" required />
      <input name="prefix" placeholder="Message prefix (optional)" />
      <input name="delaySec" type="number" placeholder="Delay seconds" required />
      <button type="submit">Send Messages</button>
    </form>
  `);
});

/* ================= PAIRING CODE ================= */
app.get("/code", async (req, res) => {
  try {
    const number = req.query.number.replace(/[^0-9]/g, "");
    if (!number) return res.send("Invalid number");

    const sessionId = "session_" + Date.now();
    const sessionPath = path.join(TEMP_DIR, sessionId);

    const { state, saveCreds } = await useMultiFileAuthState(sessionPath);
    const { version } = await fetchLatestBaileysVersion();

    const sock = makeWASocket({
      version,
      logger: pino({ level: "silent" }),
      browser: Browsers.ubuntu("Chrome"),
      auth: {
        creds: state.creds,
        keys: makeCacheableSignalKeyStore(state.keys, pino({ level: "silent" }))
      }
    });

    sock.ev.on("creds.update", saveCreds);

    if (!sock.authState.creds.registered) {
      await delay(1500);
      const code = await sock.requestPairingCode(number);
      activeClients.set(sessionId, sock);

      return res.send(`
        <h2>Pairing Code:</h2>
        <h1>${code}</h1>
        <p>WhatsApp → Linked Devices → Enter Code</p>
        <a href="/">Back</a>
      `);
    }

    res.send("Already paired");

  } catch (err) {
    res.send("Error: " + err.message);
  }
});

/* ================= SEND MESSAGE ================= */
app.post("/send-message", upload.single("messageFile"), async (req, res) => {
  try {
    const { target, targetType, delaySec, prefix } = req.body;
    if (!target || !delaySec || !req.file)
      return res.send("Missing fields");

    const sock = [...activeClients.values()][0];
    if (!sock) return res.send("No WhatsApp session");

    const messages = fs
      .readFileSync(req.file.path, "utf-8")
      .split("\n")
      .filter(Boolean);

    const jid =
      targetType === "group"
        ? target + "@g.us"
        : target + "@s.whatsapp.net";

    res.send("Started sending messages ✅");

    for (const m of messages) {
      await sock.sendMessage(jid, {
        text: prefix ? prefix + " " + m : m
      });
      await delay(Number(delaySec) * 1000);
    }

  } catch (err) {
    console.error(err);
  }
});

/* ================= START SERVER ================= */
app.listen(PORT, () => {
  console.log("✅ Server running on port " + PORT);
});

/* ================= CLEAN EXIT ================= */
process.on("SIGINT", () => {
  console.log("Shutting down...");
  process.exit(0);
});
