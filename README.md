import { useState, useRef, useEffect } from "react";

const initialContacts = [
  {
    id: 1,
    name: "Alice Johnson",
    avatar: "AJ",
    color: "#25D366",
    lastMessage: "See you tomorrow! 👋",
    time: "10:42 AM",
    unread: 2,
    online: true,
    messages: [
      { id: 1, text: "Hey! Are we still on for tomorrow?", sent: false, time: "10:30 AM", status: "read" },
      { id: 2, text: "Absolutely! I'll be there at 10", sent: true, time: "10:31 AM", status: "read" },
      { id: 3, text: "Perfect 🎉 I'm so excited!", sent: false, time: "10:40 AM", status: "read" },
      { id: 4, text: "See you tomorrow! 👋", sent: false, time: "10:42 AM", status: "read" },
    ],
  },
  {
    id: 2,
    name: "Bob Martinez",
    avatar: "BM",
    color: "#128C7E",
    lastMessage: "Did you check the report?",
    time: "9:15 AM",
    unread: 0,
    online: false,
    messages: [
      { id: 1, text: "Morning! Did you have a chance to look at the Q3 report?", sent: false, time: "9:10 AM", status: "read" },
      { id: 2, text: "Just opened it, give me a sec", sent: true, time: "9:12 AM", status: "read" },
      { id: 3, text: "Did you check the report?", sent: false, time: "9:15 AM", status: "read" },
    ],
  },
  {
    id: 3,
    name: "Clara Dev Team 🚀",
    avatar: "CD",
    color: "#075E54",
    lastMessage: "Build succeeded ✅",
    time: "Yesterday",
    unread: 5,
    online: true,
    messages: [
      { id: 1, text: "Pushing the new feature branch now", sent: true, time: "Yesterday 3:00 PM", status: "read" },
      { id: 2, text: "Running tests... 🤞", sent: false, time: "Yesterday 3:05 PM", status: "read" },
      { id: 3, text: "Build succeeded ✅", sent: false, time: "Yesterday 3:10 PM", status: "read" },
    ],
  },
  {
    id: 4,
    name: "David Kim",
    avatar: "DK",
    color: "#34B7F1",
    lastMessage: "Sounds good to me!",
    time: "Yesterday",
    unread: 0,
    online: false,
    messages: [
      { id: 1, text: "Lunch at the usual spot?", sent: true, time: "Yesterday 12:00 PM", status: "read" },
      { id: 2, text: "Sounds good to me!", sent: false, time: "Yesterday 12:03 PM", status: "read" },
    ],
  },
  {
    id: 5,
    name: "Emma Wilson",
    avatar: "EW",
    color: "#ECE5DD",
    lastMessage: "Photo 📷",
    time: "Monday",
    unread: 0,
    online: true,
    messages: [
      { id: 1, text: "Look at this view! 😍", sent: false, time: "Monday 6:00 PM", status: "read" },
      { id: 2, text: "Photo 📷", sent: false, time: "Monday 6:01 PM", status: "read" },
    ],
  },
];

const CheckIcon = ({ status }) => {
  if (status === "sent") return <span style={{ color: "#aaa", fontSize: 12 }}>✓</span>;
  if (status === "delivered") return <span style={{ color: "#aaa", fontSize: 12 }}>✓✓</span>;
  if (status === "read") return <span style={{ color: "#53bdeb", fontSize: 12 }}>✓✓</span>;
  return null;
};

export default function WhatsAppClone() {
  const [contacts, setContacts] = useState(initialContacts);
  const [activeId, setActiveId] = useState(null);
  const [input, setInput] = useState("");
  const [search, setSearch] = useState("");
  const [showInfo, setShowInfo] = useState(false);
  const [showSidebar, setShowSidebar] = useState(true);
  const messagesEndRef = useRef(null);
  const inputRef = useRef(null);

  const active = contacts.find((c) => c.id === activeId);

  useEffect(() => {
    if (messagesEndRef.current) {
      messagesEndRef.current.scrollIntoView({ behavior: "smooth" });
    }
  }, [active?.messages?.length]);

  const sendMessage = () => {
    if (!input.trim() || !activeId) return;
    const newMsg = {
      id: Date.now(),
      text: input.trim(),
      sent: true,
      time: new Date().toLocaleTimeString([], { hour: "2-digit", minute: "2-digit" }),
      status: "sent",
    };
    setContacts((prev) =>
      prev.map((c) =>
        c.id === activeId
          ? { ...c, messages: [...c.messages, newMsg], lastMessage: input.trim(), time: "Now" }
          : c
      )
    );
    setInput("");
    inputRef.current?.focus();

    // Simulate delivery
    setTimeout(() => {
      setContacts((prev) =>
        prev.map((c) =>
          c.id === activeId
            ? {
                ...c,
                messages: c.messages.map((m) =>
                  m.id === newMsg.id ? { ...m, status: "delivered" } : m
                ),
              }
            : c
        )
      );
    }, 800);
  };

  const filteredContacts = contacts.filter((c) =>
    c.name.toLowerCase().includes(search.toLowerCase())
  );

  const handleKeyDown = (e) => {
    if (e.key === "Enter" && !e.shiftKey) {
      e.preventDefault();
      sendMessage();
    }
  };

  const selectContact = (id) => {
    setActiveId(id);
    setShowInfo(false);
    setContacts((prev) =>
      prev.map((c) => (c.id === id ? { ...c, unread: 0 } : c))
    );
  };

  return (
    <div style={styles.root}>
      {/* Sidebar */}
      <div style={{ ...styles.sidebar, display: showSidebar ? "flex" : "none" }}>
        {/* Sidebar Header */}
        <div style={styles.sidebarHeader}>
          <div style={styles.myAvatar}>Me</div>
          <div style={{ flex: 1 }} />
          <button style={styles.iconBtn} title="New Chat">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/></svg>
          </button>
          <button style={styles.iconBtn} title="Menu">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor"><circle cx="12" cy="5" r="1.5"/><circle cx="12" cy="12" r="1.5"/><circle cx="12" cy="19" r="1.5"/></svg>
          </button>
        </div>

        {/* Search */}
        <div style={styles.searchWrap}>
          <div style={styles.searchIcon}>
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="#aaa" strokeWidth="2"><circle cx="11" cy="11" r="8"/><path d="M21 21l-4.35-4.35"/></svg>
          </div>
          <input
            style={styles.searchInput}
            placeholder="Search or start new chat"
            value={search}
            onChange={(e) => setSearch(e.target.value)}
          />
        </div>

        {/* Contact List */}
        <div style={styles.contactList}>
          {filteredContacts.map((c) => (
            <div
              key={c.id}
              style={{
                ...styles.contactItem,
                background: activeId === c.id ? "#202c33" : "transparent",
              }}
              onClick={() => selectContact(c.id)}
            >
              <div style={{ position: "relative" }}>
                <div style={{ ...styles.avatar, background: c.color, color: c.color === "#ECE5DD" ? "#555" : "#fff" }}>
                  {c.avatar}
                </div>
                {c.online && <div style={styles.onlineDot} />}
              </div>
              <div style={styles.contactInfo}>
                <div style={styles.contactTop}>
                  <span style={styles.contactName}>{c.name}</span>
                  <span style={styles.contactTime}>{c.time}</span>
                </div>
                <div style={styles.contactBottom}>
                  <span style={styles.lastMessage}>{c.lastMessage}</span>
                  {c.unread > 0 && <span style={styles.badge}>{c.unread}</span>}
                </div>
              </div>
            </div>
          ))}
        </div>
      </div>

      {/* Main Chat Area */}
      <div style={styles.main}>
        {active ? (
          <>
            {/* Chat Header */}
            <div style={styles.chatHeader}>
              <button style={styles.iconBtn} onClick={() => setShowSidebar(!showSidebar)}>
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"><line x1="3" y1="12" x2="21" y2="12"/><line x1="3" y1="6" x2="21" y2="6"/><line x1="3" y1="18" x2="21" y2="18"/></svg>
              </button>
              <div style={{ position: "relative", cursor: "pointer" }} onClick={() => setShowInfo(!showInfo)}>
                <div style={{ ...styles.avatar, background: active.color, color: active.color === "#ECE5DD" ? "#555" : "#fff" }}>
                  {active.avatar}
                </div>
                {active.online && <div style={styles.onlineDot} />}
              </div>
              <div style={{ flex: 1, cursor: "pointer" }} onClick={() => setShowInfo(!showInfo)}>
                <div style={styles.chatHeaderName}>{active.name}</div>
                <div style={styles.chatHeaderStatus}>
                  {active.online ? "online" : "last seen recently"}
                </div>
              </div>
              <button style={styles.iconBtn}>
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"><circle cx="11" cy="11" r="8"/><path d="M21 21l-4.35-4.35"/></svg>
              </button>
              <button style={styles.iconBtn}>
                <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor"><circle cx="12" cy="5" r="1.5"/><circle cx="12" cy="12" r="1.5"/><circle cx="12" cy="19" r="1.5"/></svg>
              </button>
            </div>

            {/* Messages */}
            <div style={styles.messagesArea}>
              <div style={styles.messagesInner}>
                {active.messages.map((msg, i) => (
                  <div
                    key={msg.id}
                    style={{
                      ...styles.msgRow,
                      justifyContent: msg.sent ? "flex-end" : "flex-start",
                      animationDelay: `${i * 0.03}s`,
                    }}
                  >
                    <div style={msg.sent ? styles.msgSent : styles.msgReceived}>
                      <span style={styles.msgText}>{msg.text}</span>
                      <span style={styles.msgMeta}>
                        {msg.time} {msg.sent && <CheckIcon status={msg.status} />}
                      </span>
                    </div>
                  </div>
                ))}
                <div ref={messagesEndRef} />
              </div>
            </div>

            {/* Input Bar */}
            <div style={styles.inputBar}>
              <button style={styles.iconBtn}>
                <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="#aaa" strokeWidth="2"><circle cx="12" cy="12" r="10"/><path d="M8 14s1.5 2 4 2 4-2 4-2"/><line x1="9" y1="9" x2="9.01" y2="9"/><line x1="15" y1="9" x2="15.01" y2="9"/></svg>
              </button>
              <button style={styles.iconBtn}>
                <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="#aaa" strokeWidth="2"><path d="M21.44 11.05l-9.19 9.19a6 6 0 0 1-8.49-8.49l9.19-9.19a4 4 0 0 1 5.66 5.66l-9.2 9.19a2 2 0 0 1-2.83-2.83l8.49-8.48"/></svg>
              </button>
              <input
                ref={inputRef}
                style={styles.textInput}
                placeholder="Type a message"
                value={input}
                onChange={(e) => setInput(e.target.value)}
                onKeyDown={handleKeyDown}
              />
              {input.trim() ? (
                <button style={styles.sendBtn} onClick={sendMessage}>
                  <svg width="20" height="20" viewBox="0 0 24 24" fill="white"><path d="M2.01 21L23 12 2.01 3 2 10l15 2-15 2z"/></svg>
                </button>
              ) : (
                <button style={styles.sendBtn}>
                  <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="white" strokeWidth="2"><path d="M12 1a3 3 0 0 0-3 3v8a3 3 0 0 0 6 0V4a3 3 0 0 0-3-3z"/><path d="M19 10v2a7 7 0 0 1-14 0v-2"/><line x1="12" y1="19" x2="12" y2="23"/><line x1="8" y1="23" x2="16" y2="23"/></svg>
                </button>
              )}
            </div>

            {/* Contact Info Panel */}
            {showInfo && (
              <div style={styles.infoPanel}>
                <div style={styles.infoPanelHeader}>
                  <button style={{ ...styles.iconBtn, color: "#aaa" }} onClick={() => setShowInfo(false)}>✕</button>
                  <span style={{ color: "#e9edef", fontWeight: 600 }}>Contact Info</span>
                </div>
                <div style={styles.infoContent}>
                  <div style={{ ...styles.bigAvatar, background: active.color, color: active.color === "#ECE5DD" ? "#555" : "#fff" }}>
                    {active.avatar}
                  </div>
                  <div style={{ color: "#e9edef", fontSize: 22, fontWeight: 600, marginTop: 12 }}>{active.name}</div>
                  <div style={{ color: active.online ? "#25D366" : "#8696a0", fontSize: 13, marginTop: 4 }}>
                    {active.online ? "online" : "last seen recently"}
                  </div>
                  <div style={styles.infoSection}>
                    <div style={{ color: "#8696a0", fontSize: 12, marginBottom: 4 }}>About</div>
                    <div style={{ color: "#e9edef", fontSize: 14 }}>Hey there! I am using WhatsApp.</div>
                  </div>
                  <div style={styles.infoSection}>
                    <div style={{ color: "#8696a0", fontSize: 12, marginBottom: 4 }}>Media</div>
                    <div style={{ color: "#e9edef", fontSize: 14 }}>{active.messages.length} messages</div>
                  </div>
                </div>
              </div>
            )}
          </>
        ) : (
          <div style={styles.welcome}>
            <div style={styles.welcomeIcon}>
              <svg width="80" height="80" viewBox="0 0 24 24" fill="none" stroke="#ffffff30" strokeWidth="1"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/></svg>
            </div>
            <div style={styles.welcomeTitle}>WhatsApp Web</div>
            <div style={styles.welcomeSubtitle}>
              Send and receive messages without keeping your phone online.
              <br />Use WhatsApp on up to 4 linked devices and 1 phone at the same time.
            </div>
            <div style={styles.welcomeHint}>
              🔒 Your personal messages are end-to-end encrypted
            </div>
          </div>
        )}
      </div>

      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Segoe+UI:wght@400;500;600&display=swap');
        * { box-sizing: border-box; margin: 0; padding: 0; }
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: #ffffff20; border-radius: 3px; }
        @keyframes fadeSlide {
          from { opacity: 0; transform: translateY(6px); }
          to { opacity: 1; transform: translateY(0); }
        }
        @keyframes msgIn {
          from { opacity: 0; transform: scale(0.96); }
          to { opacity: 1; transform: scale(1); }
        }
      `}</style>
    </div>
  );
}

const styles = {
  root: {
    display: "flex",
    height: "100vh",
    width: "100%",
    background: "#111b21",
    fontFamily: "'Segoe UI', system-ui, sans-serif",
    overflow: "hidden",
  },
  sidebar: {
    width: 360,
    minWidth: 280,
    borderRight: "1px solid #2a3942",
    flexDirection: "column",
    background: "#111b21",
    flexShrink: 0,
  },
  sidebarHeader: {
    display: "flex",
    alignItems: "center",
    padding: "10px 16px",
    background: "#202c33",
    gap: 8,
  },
  myAvatar: {
    width: 40,
    height: 40,
    borderRadius: "50%",
    background: "#25D366",
    color: "#fff",
    display: "flex",
    alignItems: "center",
    justifyContent: "center",
    fontSize: 13,
    fontWeight: 600,
    cursor: "pointer",
  },
  avatar: {
    width: 46,
    height: 46,
    borderRadius: "50%",
    display: "flex",
    alignItems: "center",
    justifyContent: "center",
    fontSize: 14,
    fontWeight: 700,
    flexShrink: 0,
  },
  bigAvatar: {
    width: 100,
    height: 100,
    borderRadius: "50%",
    display: "flex",
    alignItems: "center",
    justifyContent: "center",
    fontSize: 32,
    fontWeight: 700,
  },
  onlineDot: {
    position: "absolute",
    bottom: 2,
    right: 2,
    width: 11,
    height: 11,
    borderRadius: "50%",
    background: "#25D366",
    border: "2px solid #111b21",
  },
  iconBtn: {
    background: "none",
    border: "none",
    cursor: "pointer",
    color: "#aebac1",
    padding: 6,
    borderRadius: "50%",
    display: "flex",
    alignItems: "center",
    justifyContent: "center",
    transition: "background 0.2s",
  },
  searchWrap: {
    position: "relative",
    padding: "8px 12px",
    background: "#111b21",
  },
  searchIcon: {
    position: "absolute",
    left: 22,
    top: "50%",
    transform: "translateY(-50%)",
    pointerEvents: "none",
  },
  searchInput: {
    width: "100%",
    padding: "8px 12px 8px 38px",
    background: "#202c33",
    border: "none",
    borderRadius: 8,
    color: "#e9edef",
    fontSize: 14,
    outline: "none",
  },
  contactList: {
    flex: 1,
    overflowY: "auto",
  },
  contactItem: {
    display: "flex",
    alignItems: "center",
    padding: "10px 16px",
    gap: 12,
    cursor: "pointer",
    transition: "background 0.15s",
    borderBottom: "1px solid #1f2c33",
    position: "relative",
  },
  contactInfo: {
    flex: 1,
    overflow: "hidden",
  },
  contactTop: {
    display: "flex",
    justifyContent: "space-between",
    alignItems: "center",
    marginBottom: 3,
  },
  contactName: {
    color: "#e9edef",
    fontSize: 15,
    fontWeight: 500,
  },
  contactTime: {
    color: "#8696a0",
    fontSize: 12,
  },
  contactBottom: {
    display: "flex",
    justifyContent: "space-between",
    alignItems: "center",
  },
  lastMessage: {
    color: "#8696a0",
    fontSize: 13,
    overflow: "hidden",
    textOverflow: "ellipsis",
    whiteSpace: "nowrap",
    flex: 1,
  },
  badge: {
    background: "#25D366",
    color: "#fff",
    borderRadius: 12,
    padding: "2px 6px",
    fontSize: 11,
    fontWeight: 600,
    minWidth: 20,
    textAlign: "center",
    marginLeft: 8,
  },
  main: {
    flex: 1,
    display: "flex",
    flexDirection: "column",
    position: "relative",
    background: "#0b141a",
    backgroundImage: `url("data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 60 60' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%23ffffff' fill-opacity='0.02'%3E%3Ccircle cx='30' cy='30' r='2'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E")`,
  },
  chatHeader: {
    display: "flex",
    alignItems: "center",
    padding: "8px 16px",
    background: "#202c33",
    gap: 12,
    zIndex: 10,
    borderBottom: "1px solid #2a3942",
  },
  chatHeaderName: {
    color: "#e9edef",
    fontSize: 15,
    fontWeight: 600,
  },
  chatHeaderStatus: {
    color: "#8696a0",
    fontSize: 12,
  },
  messagesArea: {
    flex: 1,
    overflowY: "auto",
    padding: "16px",
    display: "flex",
    flexDirection: "column",
  },
  messagesInner: {
    maxWidth: 720,
    margin: "0 auto",
    width: "100%",
    display: "flex",
    flexDirection: "column",
    gap: 4,
  },
  msgRow: {
    display: "flex",
    animation: "fadeSlide 0.2s ease forwards",
  },
  msgSent: {
    background: "#005c4b",
    color: "#e9edef",
    borderRadius: "8px 0 8px 8px",
    padding: "6px 10px 6px 10px",
    maxWidth: "65%",
    fontSize: 14,
    lineHeight: 1.5,
    boxShadow: "0 1px 2px #0003",
    display: "flex",
    flexDirection: "column",
    animation: "msgIn 0.15s ease",
  },
  msgReceived: {
    background: "#202c33",
    color: "#e9edef",
    borderRadius: "0 8px 8px 8px",
    padding: "6px 10px 6px 10px",
    maxWidth: "65%",
    fontSize: 14,
    lineHeight: 1.5,
    boxShadow: "0 1px 2px #0003",
    display: "flex",
    flexDirection: "column",
    animation: "msgIn 0.15s ease",
  },
  msgText: {
    wordBreak: "break-word",
  },
  msgMeta: {
    color: "#8696a0",
    fontSize: 11,
    marginTop: 3,
    alignSelf: "flex-end",
    display: "flex",
    alignItems: "center",
    gap: 3,
  },
  inputBar: {
    display: "flex",
    alignItems: "center",
    padding: "8px 16px",
    background: "#202c33",
    gap: 8,
    borderTop: "1px solid #2a3942",
  },
  textInput: {
    flex: 1,
    background: "#2a3942",
    border: "none",
    borderRadius: 8,
    padding: "10px 16px",
    color: "#e9edef",
    fontSize: 14,
    outline: "none",
    resize: "none",
  },
  sendBtn: {
    width: 40,
    height: 40,
    borderRadius: "50%",
    background: "#00a884",
    border: "none",
    cursor: "pointer",
    display: "flex",
    alignItems: "center",
    justifyContent: "center",
    flexShrink: 0,
    transition: "background 0.2s",
  },
  welcome: {
    flex: 1,
    display: "flex",
    flexDirection: "column",
    alignItems: "center",
    justifyContent: "center",
    gap: 16,
    padding: 32,
  },
  welcomeIcon: {
    marginBottom: 8,
  },
  welcomeTitle: {
    color: "#e9edef",
    fontSize: 30,
    fontWeight: 300,
  },
  welcomeSubtitle: {
    color: "#8696a0",
    fontSize: 14,
    textAlign: "center",
    lineHeight: 1.8,
    maxWidth: 400,
  },
  welcomeHint: {
    color: "#8696a0",
    fontSize: 12,
    borderTop: "1px solid #2a3942",
    paddingTop: 16,
    marginTop: 8,
  },
  infoPanel: {
    position: "absolute",
    top: 0,
    right: 0,
    width: 320,
    height: "100%",
    background: "#111b21",
    borderLeft: "1px solid #2a3942",
    display: "flex",
    flexDirection: "column",
    zIndex: 20,
    animation: "fadeSlide 0.2s ease",
  },
  infoPanelHeader: {
    display: "flex",
    alignItems: "center",
    gap: 16,
    padding: "16px 20px",
    background: "#202c33",
  },
  infoContent: {
    display: "flex",
    flexDirection: "column",
    alignItems: "center",
    padding: 24,
    gap: 4,
    overflowY: "auto",
  },
  infoSection: {
    width: "100%",
    background: "#202c33",
    borderRadius: 8,
    padding: "12px 16px",
    marginTop: 16,
  },
};
