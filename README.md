# torinbell.github.io

import { useState, useEffect, useCallback, useRef } from "react";

/* ═══════════════════════════════════════════════════════════════
   TORIN BELL STUDIO — "Professional Journal" Portfolio
   6-page flip navigation · Dot grid paper · Premium leather-bound
   ═══════════════════════════════════════════════════════════════ */

const PAGES = [
  { id: 0, title: "Cover", tab: "About", color: "#5B7B9A" },
  { id: 1, title: "Services", tab: "Services", color: "#7B6B5A" },
  { id: 2, title: "Portfolio", tab: "Works", color: "#5A7B6B" },
  { id: 3, title: "Recent", tab: "Recent", color: "#7B5A6B" },
  { id: 4, title: "Shop", tab: "Shop", color: "#6B5A7B" },
  { id: 5, title: "Contact", tab: "Contact", color: "#5A6B7B" },
];

const SERVICES = [
  { title: "3D Visualization", desc: "Photorealistic renders, architectural viz, product shots", icon: "◇" },
  { title: "Motion Design", desc: "Animated logos, explainers, social media content", icon: "△" },
  { title: "3D Printing", desc: "Custom FDM + resin prints, prototyping, art pieces", icon: "□" },
  { title: "Photography", desc: "Product, editorial, and environmental photography", icon: "○" },
  { title: "Creative Direction", desc: "Brand identity, visual strategy, art direction", icon: "⬡" },
  { title: "Custom Commission", desc: "Bespoke creative projects tailored to your vision", icon: "☆" },
];

const PORTFOLIO = [
  { title: "Neon Horizon", cat: "3D Render", img: "https://images.unsplash.com/photo-1633356122544-f134324a6cee?w=500&q=80", year: "2025" },
  { title: "Chrome Reflection", cat: "3D Render", img: "https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?w=500&q=80", year: "2025" },
  { title: "Urban Decay", cat: "Photography", img: "https://images.unsplash.com/photo-1506905925346-21bda4d32df4?w=500&q=80", year: "2024" },
  { title: "Liquid Metal", cat: "3D Print", img: "https://images.unsplash.com/photo-1634017839464-5c339ebe3cb4?w=500&q=80", year: "2024" },
  { title: "Desert Light", cat: "Photography", img: "https://images.unsplash.com/photo-1509316975850-ff9c5deb0cd9?w=500&q=80", year: "2024" },
  { title: "Obsidian Form", cat: "3D Print", img: "https://images.unsplash.com/photo-1620641788421-7a1c342ea42e?w=500&q=80", year: "2025" },
];

const SHOP_ITEMS = [
  { title: "Abstract Texture Pack", price: "$29", type: "Digital" },
  { title: "Geometric Sculpture", price: "$89", type: "3D Print" },
  { title: "Cinematic LUT Pack", price: "$19", type: "Digital" },
  { title: "Custom Commission", price: "From $150", type: "Service" },
];

const RECENT_ITEMS = [
  { title: "Studio Tour 2026", type: "video", img: "https://images.unsplash.com/photo-1550745165-9bc0b252726f?w=400&q=80" },
  { title: "New Resin Print Process", type: "post", img: "https://images.unsplash.com/photo-1558618666-fcd25c85f82e?w=400&q=80" },
  { title: "Client Delivery — Arch Viz", type: "post", img: "https://images.unsplash.com/photo-1486325212027-8081e485255e?w=400&q=80" },
  { title: "Behind the Render", type: "video", img: "https://images.unsplash.com/photo-1579546929518-9e396f3cc809?w=400&q=80" },
];

// ── Photo corner component ──
function PhotoCorner({ position }) {
  const styles = {
    topLeft: { top: 0, left: 0, borderTop: "2px solid #2C3E50", borderLeft: "2px solid #2C3E50" },
    topRight: { top: 0, right: 0, borderTop: "2px solid #2C3E50", borderRight: "2px solid #2C3E50" },
    bottomLeft: { bottom: 0, left: 0, borderBottom: "2px solid #2C3E50", borderLeft: "2px solid #2C3E50" },
    bottomRight: { bottom: 0, right: 0, borderBottom: "2px solid #2C3E50", borderRight: "2px solid #2C3E50" },
  };
  return <div style={{ position: "absolute", width: 12, height: 12, ...styles[position], opacity: 0.3 }} />;
}

function PhotoFrame({ children, style = {} }) {
  return (
    <div style={{ position: "relative", padding: 3, ...style }}>
      <PhotoCorner position="topLeft" />
      <PhotoCorner position="topRight" />
      <PhotoCorner position="bottomLeft" />
      <PhotoCorner position="bottomRight" />
      {children}
    </div>
  );
}

// ══════════════════════════════════════════════════════
export default function App() {
  const [currentPage, setCurrentPage] = useState(0);
  const [prevPage, setPrevPage] = useState(-1);
  const [direction, setDirection] = useState(1);
  const [isFlipping, setIsFlipping] = useState(false);
  const [visitorName, setVisitorName] = useState("");
  const [time, setTime] = useState(new Date());
  const [cornerHover, setCornerHover] = useState(false);
  const [chatOpen, setChatOpen] = useState(false);
  const touchStart = useRef(null);

  useEffect(() => {
    const t = setInterval(() => setTime(new Date()), 1000);
    return () => clearInterval(t);
  }, []);

  const goToPage = useCallback((idx) => {
    if (idx === currentPage || isFlipping || idx < 0 || idx > 5) return;
    setDirection(idx > currentPage ? 1 : -1);
    setPrevPage(currentPage);
    setIsFlipping(true);
    setCurrentPage(idx);
    setTimeout(() => { setIsFlipping(false); setPrevPage(-1); }, 600);
  }, [currentPage, isFlipping]);

  const nextPage = () => goToPage(currentPage + 1);
  const prevPageFn = () => goToPage(currentPage - 1);

  // Keyboard navigation
  useEffect(() => {
    const onKey = (e) => {
      if (e.key === "ArrowRight" || e.key === "ArrowDown") nextPage();
      if (e.key === "ArrowLeft" || e.key === "ArrowUp") prevPageFn();
    };
    window.addEventListener("keydown", onKey);
    return () => window.removeEventListener("keydown", onKey);
  }, [currentPage, isFlipping]);

  // Touch/swipe for mobile
  const onTouchStart = (e) => { touchStart.current = e.touches[0].clientY; };
  const onTouchEnd = (e) => {
    if (!touchStart.current) return;
    const diff = touchStart.current - e.changedTouches[0].clientY;
    if (Math.abs(diff) > 60) { diff > 0 ? nextPage() : prevPageFn(); }
    touchStart.current = null;
  };

  // Tab positioning logic: tabs before current page go left, after go right
  const leftTabs = PAGES.filter(p => p.id < currentPage);
  const rightTabs = PAGES.filter(p => p.id > currentPage);

  return (
    <div
      onTouchStart={onTouchStart} onTouchEnd={onTouchEnd}
      style={{
        minHeight: "100vh", background: "#1A1A1A",
        display: "flex", alignItems: "center", justifyContent: "center",
        fontFamily: "'Inter', sans-serif", position: "relative",
        overflow: "hidden", padding: "20px 0",
      }}
    >
      <link href="https://fonts.googleapis.com/css2?family=Caveat:wght@400;500;600;700&family=Inter:wght@300;400;500;600&display=swap" rel="stylesheet" />

      <style>{`
        *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
        ::selection { background: #2C3E50; color: #FDFCF0; }
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: #ccc; border-radius: 2px; }

        .hw { font-family: 'Caveat', cursive; }

        /* Dot grid background */
        .dot-grid {
          background-image: radial-gradient(circle, #c8c4b8 0.7px, transparent 0.7px);
          background-size: 24px 24px;
          background-position: 12px 12px;
        }

        /* Page sheet */
        .journal-sheet {
          position: relative;
          width: min(820px, 92vw);
          height: min(600px, 78vh);
          background: #FDFCF0;
          border-radius: 4px;
          box-shadow:
            0 2px 4px rgba(0,0,0,0.1),
            0 8px 24px rgba(0,0,0,0.15),
            0 20px 60px rgba(0,0,0,0.2),
            inset 0 0 80px rgba(0,0,0,0.02);
          overflow: hidden;
          perspective: 1200px;
        }

        /* Page content area */
        .page-content {
          position: absolute; inset: 0;
          padding: 72px 48px 48px;
          overflow-y: auto;
          overflow-x: hidden;
        }

        /* Page flip animations */
        .page-enter-right {
          animation: flipInRight 0.55s cubic-bezier(0.23, 1, 0.32, 1) forwards;
        }
        .page-exit-left {
          animation: flipOutLeft 0.55s cubic-bezier(0.23, 1, 0.32, 1) forwards;
        }
        .page-enter-left {
          animation: flipInLeft 0.55s cubic-bezier(0.23, 1, 0.32, 1) forwards;
        }
        .page-exit-right {
          animation: flipOutRight 0.55s cubic-bezier(0.23, 1, 0.32, 1) forwards;
        }

        @keyframes flipInRight {
          from { opacity: 0; transform: translateX(30px) rotateY(-8deg) scale(0.97); }
          to { opacity: 1; transform: translateX(0) rotateY(0) scale(1); }
        }
        @keyframes flipOutLeft {
          from { opacity: 1; transform: translateX(0) rotateY(0) scale(1); }
          to { opacity: 0; transform: translateX(-30px) rotateY(8deg) scale(0.97); }
        }
        @keyframes flipInLeft {
          from { opacity: 0; transform: translateX(-30px) rotateY(8deg) scale(0.97); }
          to { opacity: 1; transform: translateX(0) rotateY(0) scale(1); }
        }
        @keyframes flipOutRight {
          from { opacity: 1; transform: translateX(0) rotateY(0) scale(1); }
          to { opacity: 0; transform: translateX(30px) rotateY(-8deg) scale(0.97); }
        }

        /* Page corner curl */
        .page-curl {
          position: absolute; bottom: 0; right: 0;
          width: 50px; height: 50px; cursor: pointer; z-index: 10;
        }
        .page-curl::after {
          content: ''; position: absolute; bottom: 0; right: 0;
          width: 0; height: 0;
          border-style: solid;
          border-width: 0 0 30px 30px;
          border-color: transparent transparent #e8e4da transparent;
          transition: all 0.4s cubic-bezier(0.23,1,0.32,1);
          box-shadow: -2px -2px 4px rgba(0,0,0,0.05);
        }
        .page-curl:hover::after {
          border-width: 0 0 44px 44px;
          border-color: transparent transparent #ddd8cc transparent;
          box-shadow: -3px -3px 8px rgba(0,0,0,0.1);
        }

        /* Side tabs */
        .journal-tab {
          position: absolute;
          width: 32px; height: 64px;
          display: flex; align-items: center; justify-content: center;
          writing-mode: vertical-rl; text-orientation: mixed;
          font-family: 'Caveat', cursive; font-size: 13px;
          color: #fff; border: none; cursor: pointer;
          transition: all 0.3s cubic-bezier(0.23,1,0.32,1);
          z-index: 20; letter-spacing: 0.5px;
          text-shadow: 0 1px 2px rgba(0,0,0,0.2);
        }
        .journal-tab.tab-left {
          left: -28px; border-radius: 4px 0 0 4px;
          opacity: 0.6;
        }
        .journal-tab.tab-left:hover { left: -32px; opacity: 0.9; }
        .journal-tab.tab-right {
          right: -28px; border-radius: 0 4px 4px 0;
          opacity: 0.7;
        }
        .journal-tab.tab-right:hover { right: -32px; opacity: 1; }

        /* Section title style */
        .section-num {
          font-family: 'Caveat', cursive; font-size: 13px;
          color: #999; letter-spacing: 3px; text-transform: uppercase;
          margin-bottom: 4px;
        }
        .section-title {
          font-family: 'Caveat', cursive; font-size: 38px;
          color: #2C3E50; font-weight: 600; line-height: 1.1;
          margin-bottom: 4px;
        }
        .section-rule {
          width: 60px; height: 1.5px; background: #2C3E50;
          margin: 8px 0 20px; border-radius: 1px;
        }

        /* Paper grain overlay for images */
        .paper-grain {
          position: relative; overflow: hidden;
        }
        .paper-grain::after {
          content: ''; position: absolute; inset: 0;
          background: #FDFCF0; mix-blend-mode: multiply;
          opacity: 0.35; pointer-events: none;
          transition: opacity 0.5s ease;
        }
        .paper-grain:hover::after { opacity: 0; }

        /* Input field */
        .journal-input {
          background: transparent; border: none;
          border-bottom: 1px solid #c8c4b8;
          padding: 4px 0; outline: none;
          font-family: 'Caveat', cursive;
          color: #2C3E50; transition: border-color 0.3s;
        }
        .journal-input:focus { border-color: #2C3E50; }
        .journal-input::placeholder { color: #bbb; }

        /* Ink link hover */
        .ink-link {
          color: #2C3E50; text-decoration: none; position: relative;
          font-family: 'Caveat', cursive; font-size: 17px; cursor: pointer;
        }
        .ink-link::after {
          content: ''; position: absolute; bottom: -1px; left: 0;
          width: 0; height: 1.5px; background: #2C3E50;
          transition: width 0.35s cubic-bezier(0.23,1,0.32,1);
          border-radius: 1px; transform: rotate(-0.3deg);
        }
        .ink-link:hover::after { width: 100%; }

        /* Bottom nav for mobile */
        @media (max-width: 768px) {
          .journal-sheet { width: 96vw; height: 85vh; border-radius: 6px 6px 0 0; }
          .page-content { padding: 64px 24px 80px; }
          .journal-tab { display: none !important; }
          .mobile-nav { display: flex !important; }
          .services-grid { grid-template-columns: 1fr !important; }
          .portfolio-grid { grid-template-columns: 1fr 1fr !important; }
          .recent-grid { grid-template-columns: 1fr !important; }
          .shop-grid { grid-template-columns: 1fr !important; }
          .contact-grid { grid-template-columns: 1fr !important; }
          .section-title { font-size: 30px !important; }
        }
      `}</style>

      {/* ═══ JOURNAL SHEET ═══ */}
      <div className="journal-sheet" style={{ position: "relative" }}>

        {/* ── Global Header (persistent across all pages) ── */}
        <div style={{
          position: "absolute", top: 0, left: 0, right: 0, zIndex: 30,
          padding: "14px 48px", display: "flex", justifyContent: "space-between",
          alignItems: "center", borderBottom: "1px solid rgba(0,0,0,0.05)",
          background: "rgba(253,252,240,0.95)", backdropFilter: "blur(8px)",
        }}>
          <div style={{ display: "flex", alignItems: "center", gap: 6 }}>
            <span style={{ fontSize: 11, color: "#999", letterSpacing: 1 }}>NAME:</span>
            <input
              className="journal-input hw"
              value={visitorName}
              onChange={(e) => setVisitorName(e.target.value)}
              placeholder="sign here..."
              style={{ fontSize: 20, width: 180 }}
            />
          </div>
          <div style={{ textAlign: "right" }}>
            <div className="hw" style={{ fontSize: 16, color: "#2C3E50" }}>
              {time.toLocaleDateString("en-US", { month: "short", day: "numeric", year: "numeric" })}
            </div>
            <div style={{ fontSize: 11, color: "#999", fontVariantNumeric: "tabular-nums" }}>
              {time.toLocaleTimeString("en-US", { hour: "2-digit", minute: "2-digit", second: "2-digit" })}
            </div>
          </div>
        </div>

        {/* ── Left tabs (pages already "turned") ── */}
        {leftTabs.map((tab, i) => (
          <button key={tab.id} className="journal-tab tab-left"
            style={{ background: tab.color, top: 90 + i * 72 }}
            onClick={() => goToPage(tab.id)}>
            {tab.tab}
          </button>
        ))}

        {/* ── Right tabs (pages ahead) ── */}
        {rightTabs.map((tab, i) => (
          <button key={tab.id} className="journal-tab tab-right"
            style={{ background: tab.color, top: 90 + i * 72 }}
            onClick={() => goToPage(tab.id)}>
            {tab.tab}
          </button>
        ))}

        {/* ── Page corner curl (next page hint) ── */}
        {currentPage < 5 && (
          <div className="page-curl"
            onMouseEnter={() => setCornerHover(true)}
            onMouseLeave={() => setCornerHover(false)}
            onClick={nextPage}
          />
        )}

        {/* ── Exiting page ── */}
        {prevPage >= 0 && (
          <div className={`page-content dot-grid ${direction > 0 ? "page-exit-left" : "page-exit-right"}`}
            style={{ zIndex: 5 }}>
            <PageRenderer page={prevPage} name={visitorName} />
          </div>
        )}

        {/* ── Active page ── */}
        <div
          key={currentPage}
          className={`page-content dot-grid ${isFlipping ? (direction > 0 ? "page-enter-right" : "page-enter-left") : ""}`}
          style={{ zIndex: 10 }}
        >
          <PageRenderer page={currentPage} name={visitorName} goToPage={goToPage} />
        </div>

        {/* ── Page number ── */}
        <div style={{
          position: "absolute", bottom: 14, left: "50%", transform: "translateX(-50%)",
          zIndex: 30, display: "flex", alignItems: "center", gap: 16,
        }}>
          <button onClick={prevPageFn} disabled={currentPage === 0}
            style={{ background: "none", border: "none", color: currentPage === 0 ? "#ddd" : "#999",
              fontSize: 18, cursor: currentPage === 0 ? "default" : "pointer" }}>
            ←
          </button>
          <span className="hw" style={{ fontSize: 15, color: "#999" }}>
            {currentPage + 1} / 6
          </span>
          <button onClick={nextPage} disabled={currentPage === 5}
            style={{ background: "none", border: "none", color: currentPage === 5 ? "#ddd" : "#999",
              fontSize: 18, cursor: currentPage === 5 ? "default" : "pointer" }}>
            →
          </button>
        </div>

        {/* ── Mobile bottom nav ── */}
        <div className="mobile-nav" style={{
          display: "none", position: "absolute", bottom: 0, left: 0, right: 0,
          zIndex: 30, background: "rgba(253,252,240,0.95)", borderTop: "1px solid rgba(0,0,0,0.08)",
          padding: "6px 4px", justifyContent: "space-around",
        }}>
          {PAGES.map(p => (
            <button key={p.id} onClick={() => goToPage(p.id)} className="hw"
              style={{
                background: "none", border: "none", padding: "6px 8px",
                fontSize: 14, color: p.id === currentPage ? "#2C3E50" : "#aaa",
                fontWeight: p.id === currentPage ? 600 : 400,
                borderBottom: p.id === currentPage ? "2px solid #2C3E50" : "2px solid transparent",
                cursor: "pointer", fontFamily: "'Caveat', cursive",
              }}>
              {p.tab}
            </button>
          ))}
        </div>
      </div>

      {/* ── Chat toggle ── */}
      <button onClick={() => setChatOpen(!chatOpen)} style={{
        position: "fixed", bottom: 20, right: 20, zIndex: 9999,
        width: 48, height: 48, borderRadius: "50%", background: "#2C3E50",
        border: "none", color: "#FDFCF0", fontSize: 20, cursor: "pointer",
        boxShadow: "0 4px 16px rgba(0,0,0,0.3)", transition: "transform 0.3s",
        display: "flex", alignItems: "center", justifyContent: "center",
      }}>
        {chatOpen ? "×" : "✎"}
      </button>

      {chatOpen && <ChatPanel onClose={() => setChatOpen(false)} />}
    </div>
  );
}

// ══════════════════════════════════════════════════════
// PAGE RENDERER — Dispatches to the correct section
// ══════════════════════════════════════════════════════
function PageRenderer({ page, name, goToPage }) {
  switch (page) {
    case 0: return <CoverPage name={name} goToPage={goToPage} />;
    case 1: return <ServicesPage />;
    case 2: return <PortfolioPage />;
    case 3: return <RecentPage />;
    case 4: return <ShopPage />;
    case 5: return <ContactPage name={name} />;
    default: return null;
  }
}

// ── PAGE 1: COVER / ABOUT ──
function CoverPage({ name, goToPage }) {
  return (
    <div style={{ display: "flex", flexDirection: "column", justifyContent: "center", height: "100%", maxWidth: 560 }}>
      <div className="hw" style={{ fontSize: 56, color: "#2C3E50", fontWeight: 700, lineHeight: 1.05, marginBottom: 6 }}>
        Torin Bell
      </div>
      <div className="hw" style={{ fontSize: 26, color: "#7B8B9A", fontWeight: 400, fontStyle: "italic", marginBottom: 24 }}>
        Creative Studio
      </div>
      <div className="section-rule" />
      <p style={{ fontSize: 15, color: "#555", lineHeight: 1.8, marginBottom: 20, maxWidth: 440 }}>
        3D visualization, custom prints, photography, and motion design.
        Every project starts as a sketch and ships as something real — whether
        that's a rendered scene, a motion piece, or a product fresh off the print bed.
      </p>
      {name && (
        <p className="hw" style={{ fontSize: 19, color: "#7B8B9A", marginBottom: 20 }}>
          Welcome to the journal, {name}.
        </p>
      )}
      <div style={{ display: "flex", gap: 12, flexWrap: "wrap" }}>
        <button onClick={() => goToPage?.(2)} className="hw"
          style={btnStyle}>View Works →</button>
        <button onClick={() => goToPage?.(4)} className="hw"
          style={{ ...btnStyle, background: "transparent", color: "#2C3E50", border: "1.5px solid #2C3E50" }}>
          Visit Shop
        </button>
      </div>
      <div className="hw" style={{ position: "absolute", bottom: 40, right: 48, fontSize: 13, color: "#bbb", transform: "rotate(-2deg)" }}>
        est. 2018 · self-hosted at torinbell.studio
      </div>
    </div>
  );
}

// ── PAGE 2: SERVICES ──
function ServicesPage() {
  return (
    <div>
      <div className="section-num">Section 02</div>
      <div className="section-title">The Ledger</div>
      <div className="section-rule" />
      <div className="services-grid" style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16 }}>
        {SERVICES.map((s, i) => (
          <div key={i} style={{
            padding: "16px 18px", border: "1px solid #e8e4da", borderRadius: 3,
            transition: "border-color 0.3s, box-shadow 0.3s",
            cursor: "default",
          }}
            onMouseEnter={e => { e.currentTarget.style.borderColor = "#2C3E50"; e.currentTarget.style.boxShadow = "0 2px 8px rgba(0,0,0,0.06)"; }}
            onMouseLeave={e => { e.currentTarget.style.borderColor = "#e8e4da"; e.currentTarget.style.boxShadow = "none"; }}
          >
            <div style={{ display: "flex", alignItems: "center", gap: 10, marginBottom: 6 }}>
              <span style={{ fontSize: 18, color: "#2C3E50", opacity: 0.5 }}>{s.icon}</span>
              <span className="hw" style={{ fontSize: 20, color: "#2C3E50", fontWeight: 500 }}>{s.title}</span>
            </div>
            <p style={{ fontSize: 13, color: "#888", lineHeight: 1.5 }}>{s.desc}</p>
          </div>
        ))}
      </div>
      <div className="hw" style={{ marginTop: 20, fontSize: 15, color: "#999", fontStyle: "italic" }}>
        ✓ All services include revision rounds · Custom quotes within 24hrs
      </div>
    </div>
  );
}

// ── PAGE 3: PORTFOLIO ──
function PortfolioPage() {
  return (
    <div>
      <div className="section-num">Section 03</div>
      <div className="section-title">Evergreen Works</div>
      <div className="section-rule" />
      <div className="portfolio-grid" style={{ display: "grid", gridTemplateColumns: "repeat(3, 1fr)", gap: 16 }}>
        {PORTFOLIO.map((item, i) => (
          <div key={i} style={{ cursor: "pointer" }}>
            <PhotoFrame>
              <div className="paper-grain">
                <img src={item.img} alt={item.title}
                  style={{ width: "100%", height: 120, objectFit: "cover", display: "block" }}
                  loading="lazy" />
              </div>
            </PhotoFrame>
            <div style={{ padding: "6px 2px 0" }}>
              <div className="hw" style={{ fontSize: 17, color: "#2C3E50" }}>{item.title}</div>
              <div style={{ fontSize: 11, color: "#999", display: "flex", justifyContent: "space-between" }}>
                <span>{item.cat}</span><span>{item.year}</span>
              </div>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}

// ── PAGE 4: RECENT PROJECTS ──
function RecentPage() {
  const [loaded, setLoaded] = useState(false);
  useEffect(() => { const t = setTimeout(() => setLoaded(true), 1200); return () => clearTimeout(t); }, []);

  return (
    <div>
      <div className="section-num">Section 04</div>
      <div className="section-title">Dynamic Log</div>
      <div className="section-rule" />
      {!loaded ? (
        <div style={{ padding: "40px 0", textAlign: "center" }}>
          <div className="hw" style={{ fontSize: 18, color: "#bbb", fontStyle: "italic" }}>
            Syncing logbook entries...
          </div>
          <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginTop: 24 }}>
            {[1,2,3,4].map(i => (
              <div key={i} style={{ height: 130, background: "#f0ece0", borderRadius: 3,
                animation: "pulse 1.5s ease-in-out infinite", opacity: 0.6 }} />
            ))}
          </div>
          <style>{`@keyframes pulse { 0%,100% { opacity:0.4; } 50% { opacity:0.7; } }`}</style>
        </div>
      ) : (
        <>
          <div className="recent-grid" style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16 }}>
            {RECENT_ITEMS.map((item, i) => (
              <div key={i} style={{ cursor: "pointer" }}>
                <PhotoFrame>
                  <div className="paper-grain" style={{ position: "relative" }}>
                    <img src={item.img} alt={item.title}
                      style={{ width: "100%", height: 130, objectFit: "cover", display: "block" }}
                      loading="lazy" />
                    {item.type === "video" && (
                      <div style={{
                        position: "absolute", inset: 0, display: "flex", alignItems: "center",
                        justifyContent: "center", background: "rgba(0,0,0,0.15)", zIndex: 1,
                      }}>
                        <div style={{
                          width: 36, height: 36, borderRadius: "50%", background: "rgba(253,252,240,0.9)",
                          display: "flex", alignItems: "center", justifyContent: "center",
                        }}>
                          <span style={{ fontSize: 14, marginLeft: 2, color: "#2C3E50" }}>▶</span>
                        </div>
                      </div>
                    )}
                  </div>
                </PhotoFrame>
                <div style={{ padding: "6px 2px" }}>
                  <div className="hw" style={{ fontSize: 16, color: "#2C3E50" }}>{item.title}</div>
                  <div style={{ fontSize: 11, color: "#999", textTransform: "uppercase", letterSpacing: 1 }}>{item.type}</div>
                </div>
              </div>
            ))}
          </div>
          <div style={{ marginTop: 16, textAlign: "center" }}>
            <span className="ink-link">View Full Archives →</span>
          </div>
        </>
      )}
    </div>
  );
}

// ── PAGE 5: SHOP ──
function ShopPage() {
  return (
    <div>
      <div className="section-num">Section 05</div>
      <div className="section-title">The Catalog</div>
      <div className="section-rule" />
      <div className="shop-grid" style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16 }}>
        {SHOP_ITEMS.map((item, i) => (
          <div key={i} style={{
            border: "1px solid #e8e4da", borderRadius: 3, padding: 18,
            transition: "border-color 0.3s",
          }}
            onMouseEnter={e => e.currentTarget.style.borderColor = "#2C3E50"}
            onMouseLeave={e => e.currentTarget.style.borderColor = "#e8e4da"}
          >
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "start", marginBottom: 10 }}>
              <span style={{
                fontSize: 10, letterSpacing: 1.5, textTransform: "uppercase", padding: "2px 8px",
                background: item.type === "Digital" ? "rgba(44,62,80,0.08)" : item.type === "3D Print" ? "rgba(90,123,107,0.1)" : "rgba(123,90,107,0.1)",
                color: "#555", borderRadius: 2,
              }}>
                {item.type}
              </span>
              <span className="hw" style={{ fontSize: 26, color: "#2C3E50", fontWeight: 600 }}>{item.price}</span>
            </div>
            <div className="hw" style={{ fontSize: 20, color: "#2C3E50", marginBottom: 12 }}>{item.title}</div>
            <button className="hw" style={{
              width: "100%", padding: "8px 16px", background: "transparent",
              border: "1.5px solid #2C3E50", color: "#2C3E50", fontSize: 15,
              borderRadius: 2, cursor: "pointer", transition: "all 0.3s",
              fontFamily: "'Caveat', cursive",
            }}
              onMouseEnter={e => { e.target.style.background = "#2C3E50"; e.target.style.color = "#FDFCF0"; }}
              onMouseLeave={e => { e.target.style.background = "transparent"; e.target.style.color = "#2C3E50"; }}
            >
              {item.type === "Service" ? "Request Quote" : "Add to Cart"}
            </button>
          </div>
        ))}
      </div>
    </div>
  );
}

// ── PAGE 6: CONTACT ──
function ContactPage({ name }) {
  return (
    <div>
      <div className="section-num">Section 06</div>
      <div className="section-title">The Back Cover</div>
      <div className="section-rule" />
      <div className="contact-grid" style={{ display: "grid", gridTemplateColumns: "1.2fr 0.8fr", gap: 40 }}>
        <div style={{ display: "flex", flexDirection: "column", gap: 20 }}>
          {[
            { label: "Subject", placeholder: "What's this regarding?" },
            { label: "Name", placeholder: name || "Your name" },
            { label: "Email", placeholder: "your@email.com" },
          ].map(f => (
            <div key={f.label}>
              <label style={{ fontSize: 11, color: "#999", letterSpacing: 1, display: "block", marginBottom: 4 }}>
                {f.label.toUpperCase()}
              </label>
              <input className="journal-input hw" placeholder={f.placeholder}
                style={{ width: "100%", fontSize: 19 }} />
            </div>
          ))}
          <div>
            <label style={{ fontSize: 11, color: "#999", letterSpacing: 1, display: "block", marginBottom: 4 }}>
              DETAILS
            </label>
            <textarea className="journal-input hw" rows={3} placeholder="Tell me about your project..."
              style={{ width: "100%", fontSize: 19, resize: "none", fontFamily: "'Caveat', cursive" }} />
          </div>
          <button className="hw" style={btnStyle}>Turn In →</button>
        </div>
        <div style={{ display: "flex", flexDirection: "column", gap: 16, paddingTop: 4 }}>
          <div style={{ padding: 16, background: "#f6f3ea", borderRadius: 3 }}>
            <div className="hw" style={{ fontSize: 15, color: "#999", marginBottom: 6 }}>Direct</div>
            <div className="hw" style={{ fontSize: 18, color: "#2C3E50" }}>hello@torinbell.studio</div>
            <div className="hw" style={{ fontSize: 18, color: "#2C3E50" }}>@torinbellstudio</div>
          </div>
          <div style={{ padding: 16, background: "#f6f3ea", borderRadius: 3 }}>
            <div className="hw" style={{ fontSize: 15, color: "#999", marginBottom: 6 }}>Response time</div>
            <div className="hw" style={{ fontSize: 22, color: "#2C3E50" }}>Under 24 hours</div>
          </div>
          <div className="hw" style={{ fontSize: 14, color: "#bbb", fontStyle: "italic", marginTop: 8 }}>
            The chat assistant (bottom right) is available 24/7 for quick questions.
          </div>
        </div>
      </div>
    </div>
  );
}

// ── CHAT PANEL ──
function ChatPanel({ onClose }) {
  const [msgs, setMsgs] = useState([{ from: "bot", text: "Hey! Ask me about prints, commissions, or anything creative." }]);
  const [input, setInput] = useState("");
  const [typing, setTyping] = useState(false);
  const listRef = useRef(null);

  useEffect(() => { listRef.current?.scrollTo(0, listRef.current.scrollHeight); }, [msgs, typing]);

  const send = () => {
    const t = input.trim(); if (!t) return;
    setMsgs(m => [...m, { from: "user", text: t }]); setInput(""); setTyping(true);
    setTimeout(() => {
      setTyping(false);
      setMsgs(m => [...m, { from: "bot", text: "Thanks for writing! Once the FastAPI chatbot goes live, I'll answer questions about materials, pricing, and turnaround times." }]);
    }, 1200);
  };

  return (
    <div style={{
      position: "fixed", bottom: 78, right: 20, zIndex: 9999,
      width: 330, maxHeight: 420, borderRadius: 6,
      background: "#FDFCF0", border: "1.5px solid #e0dcd0",
      display: "flex", flexDirection: "column",
      boxShadow: "0 8px 32px rgba(0,0,0,0.25)", overflow: "hidden",
    }}>
      <div style={{
        padding: "10px 14px", borderBottom: "1px solid #e8e4da",
        display: "flex", justifyContent: "space-between", alignItems: "center",
      }}>
        <span className="hw" style={{ fontSize: 17, color: "#2C3E50" }}>Studio Assistant</span>
        <button onClick={onClose} style={{ background: "none", border: "none", cursor: "pointer", color: "#999", fontSize: 18 }}>×</button>
      </div>
      <div ref={listRef} style={{ flex: 1, overflowY: "auto", padding: 10, display: "flex", flexDirection: "column", gap: 8, maxHeight: 280 }}>
        {msgs.map((m, i) => (
          <div key={i} style={{
            alignSelf: m.from === "user" ? "flex-end" : "flex-start", maxWidth: "82%",
            padding: "7px 11px", borderRadius: m.from === "user" ? "10px 10px 2px 10px" : "10px 10px 10px 2px",
            background: m.from === "user" ? "#2C3E50" : "#f0ece0",
            color: m.from === "user" ? "#FDFCF0" : "#2C3E50",
            fontFamily: "'Caveat', cursive", fontSize: 16, lineHeight: 1.35,
          }}>{m.text}</div>
        ))}
        {typing && <div className="hw" style={{ fontSize: 16, color: "#bbb", padding: "4px 8px" }}>writing...</div>}
      </div>
      <div style={{ padding: "8px 10px", borderTop: "1px solid #e8e4da", display: "flex", gap: 6 }}>
        <input value={input} onChange={e => setInput(e.target.value)}
          onKeyDown={e => e.key === "Enter" && send()}
          className="journal-input hw" placeholder="Scribble a note..."
          style={{ flex: 1, fontSize: 16, border: "1px solid #e0dcd0", borderRadius: 4, padding: "6px 10px" }} />
        <button onClick={send} style={{
          width: 34, height: 34, borderRadius: 4, background: "#2C3E50",
          border: "none", color: "#FDFCF0", cursor: "pointer", fontSize: 14,
        }}>→</button>
      </div>
    </div>
  );
}

// ── Shared button style ──
const btnStyle = {
  padding: "10px 24px", background: "#2C3E50", color: "#FDFCF0",
  border: "1.5px solid #2C3E50", fontFamily: "'Caveat', cursive",
  fontSize: 17, borderRadius: 2, cursor: "pointer", transition: "all 0.3s",
};
