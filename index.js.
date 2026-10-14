
const {
  default: makeWASocket,
  useMultiFileAuthState,
  DisconnectReason
} = require("@whiskeysockets/baileys");

const P = require("pino");

async function iniciarBot() {
  const { state, saveCreds } =
    await useMultiFileAuthState("auth_info");

  const sock = makeWASocket({
    auth: state,
    logger: P({ level: "silent" }),
    browser: ["Elite Bot", "Chrome", "1.0.0"]
  });

  sock.ev.on("creds.update", saveCreds);

  sock.ev.on("connection.update", async ({ connection, lastDisconnect }) => {
    if (connection === "open") {
      console.log("Elite Bot conectado!");
    }

    if (connection === "close") {
      const status = lastDisconnect?.error?.output?.statusCode;

      if (status !== DisconnectReason.loggedOut) {
        console.log("Conexão perdida. Tentando reconectar...");
        setTimeout(iniciarBot, 5000);
      } else {
        console.log("Sessão desconectada. É preciso vincular novamente.");
      }
    }
  });

  sock.ev.on("messages.upsert", async ({ messages }) => {
    for (const msg of messages) {
      if (!msg.message || msg.key.fromMe || !msg.key.remoteJid?.endsWith("@g.us")) {
        continue;
      }

      const texto =
        msg.message.conversation ||
        msg.message.extendedTextMessage?.text ||
        "";

      const comando = texto.trim().toLowerCase();
      const grupo = msg.key.remoteJid;

      if (comando === "!bomdia") {
        await sock.sendMessage(grupo, {
          text: "Bom dia, Elite! 💜☀️ Que o dia de vocês seja incrível!"
        });
      }

      if (comando === "!boanoite") {
        await sock.sendMessage(grupo, {
          text: "Boa noite, Elite! 💜🌙 Descansem e se cuidem!"
        });
      }

      if (comando === "!regras") {
        await sock.sendMessage(grupo, {
          text: "👑 REGRAS DO ELITE 👑\n\nRespeite os participantes, evite spam e siga as orientações da administração."
        });
      }

      if (comando === "!ajuda") {
        await sock.sendMessage(grupo, {
          text: "🤖 COMANDOS DO ELITE BOT\n\n!bomdia\n!boanoite\n!regras\n!ajuda\n!todos"
        });
      }

      if (comando === "!todos") {
        const metadata = await sock.groupMetadata(grupo);
        const participantes = metadata.participants;

        await sock.sendMessage(grupo, {
          text: "📢 Atenção, Elite! 💜",
          mentions: participantes.map(p => p.id)
        });
      }
    }
  });
}

iniciarBot().catch(console.error);
