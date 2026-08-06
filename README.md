import React, { useState, useEffect, useMemo } from "react";
import { motion, AnimatePresence } from "motion/react";
import {
  Github, Linkedin, Mail, GitCommit, GitPullRequest, Star, Users,
  AlertCircle, Flame, Trophy, ExternalLink, Terminal, Server,
  Boxes, Database, Cpu, MessageSquareCode
} from "lucide-react";
import {
  AreaChart, Area, XAxis, YAxis, Tooltip, ResponsiveContainer,
  RadialBarChart, RadialBar, PolarAngleAxis
} from "recharts";

// ---------- dados reais do perfil ----------

const ROTATING_TITLES = [
  "estudante de engenharia de software",
  "construindo middleware Meta + WhatsApp + CRM",
  "co-fundador @ TecnoBoreal",
  "backend, automação, integrações",
];

const GABRIEL_OBJ = `const gabriel = {
  nome: "Gabriel",
  localizacao: "Rio Verde, GO 🇧🇷",
  formacao: "Eng. de Software — UniRV (1º período)",
  tecnico: "Dev. de Sistemas — SESI SENAI",

  linguagens: ["Python", "TypeScript", "Java", "C"],
  backend: ["FastAPI", "Django", "Node.js", "Spring Boot"],
  frontend: ["HTML", "CSS"],
  databases: ["PostgreSQL", "SQLite"],
  infra: ["Docker", "Nginx", "Fedora Linux", "Cloudflare Tunnel"],
  integracoes: ["Meta Ads API", "WhatsApp Cloud API", "Webhooks"],
  ia: ["Anthropic API", "Whisper", "Groq", "ElevenLabs"],

  agora: "middleware Meta Ads → WhatsApp → Imoview",
  objetivo: "AI Engineering",
};`;

const TECH_GROUPS = [
  {
    label: "linguagens",
    icon: Terminal,
    items: ["Python", "TypeScript", "Java", "C"],
  },
  {
    label: "backend",
    icon: Server,
    items: ["FastAPI", "Django", "Node.js", "Spring Boot"],
  },
  {
    label: "dados",
    icon: Database,
    items: ["PostgreSQL", "SQLite"],
  },
  {
    label: "infra & integrações",
    icon: Boxes,
    items: ["Docker", "Nginx", "Fedora", "Meta Ads API", "WhatsApp Cloud API"],
  },
];

const LANG_BREAKDOWN = [
  { name: "Python", value: 42, color: "#a855f7" },
  { name: "TypeScript", value: 28, color: "#7dd3fc" },
  { name: "Java", value: 18, color: "#f472b6" },
  { name: "C", value: 12, color: "#94a3b8" },
];

const ACTIVITY = [
  { month: "Set", commits: 38 },
  { month: "Out", commits: 61 },
  { month: "Nov", commits: 74 },
  { month: "Dez", commits: 45 },
  { month: "Jan", commits: 52 },
  { month: "Fev", commits: 69 },
  { month: "Mar", commits: 83 },
  { month: "Abr", commits: 71 },
  { month: "Mai", commits: 90 },
  { month: "Jun", commits: 77 },
  { month: "Jul", commits: 96 },
  { month: "Ago", commits: 58 },
];

const STATS = [
  { label: "commits", value: "1.204", icon: GitCommit },
  { label: "pull requests", value: "87", icon: GitPullRequest },
  { label: "issues", value: "34", icon: AlertCircle },
  { label: "estrelas", value: "29", icon: Star },
];

const TROPHIES = [
  { label: "Commits", value: "1.2k", icon: GitCommit },
  { label: "Repos", value: "24", icon: Boxes },
  { label: "PRs", value: "87", icon: GitPullRequest },
  { label: "Stars", value: "29", icon: Star },
  { label: "Followers", value: "18", icon: Users },
  { label: "Issues", value: "34", icon: AlertCircle },
];

const PROJECTS = [
  {
    name: "meta-whatsapp-imoview",
    desc: "Middleware Node.js/TypeScript em produção conectando Meta Ads, WhatsApp Cloud API e o CRM Imoview. Clean Architecture, DDD, fila de distribuição de leads e publicação automatizada de campanhas.",
    tags: ["Node.js", "TypeScript", "Clean Architecture", "DDD"],
    status: "em produção",
  },
  {
    name: "fazenda-os-agro",
    desc: "DSL para automação de fazenda inteligente — interpretador, compilador de bytecode e VM próprios, construídos do zero em Python. Projeto acadêmico (ESW449).",
    tags: ["Python", "Compilers", "VM"],
    status: "acadêmico",
  },
  {
    name: "nexus-contacts",
    desc: "Gerenciador de contatos desktop em Pygame com UI dark customizada e persistência local via SQLite.",
    tags: ["Python", "Pygame", "SQLite"],
    status: "concluído",
  },
  {
    name: "quest-log",
    desc: "App de gestão de tarefas gamificado, estilo RPG — sistema de XP, níveis e progressão sobre SQLite.",
    tags: ["Python", "Pygame", "SQLite"],
    status: "concluído",
  },
];

const QUOTES = [
  { text: "Programas devem ser escritos para pessoas lerem, e só incidentalmente para máquinas executarem.", author: "Harold Abelson" },
  { text: "A persistência realiza o impossível.", author: "provérbio" },
  { text: "Primeiro resolva o problema. Depois escreva o código.", author: "John Johnson" },
  { text: "Todo grande desenvolvedor que você conhece chegou lá resolvendo os problemas que tinha, não os que imaginava ter.", author: "Anônimo" },
];

// ---------- utilitários ----------

function useTypingRotation(strings, typingMs = 45, holdMs = 1800, deletingMs = 25) {
  const [index, setIndex] = useState(0);
  const [text, setText] = useState("");
  const [phase, setPhase] = useState("typing"); // typing | holding | deleting

  useEffect(() => {
    const current = strings[index];
    let timeout;

    if (phase === "typing") {
      if (text.length < current.length) {
        timeout = setTimeout(() => setText(current.slice(0, text.length + 1)), typingMs);
      } else {
        timeout = setTimeout(() => setPhase("holding"), holdMs);
      }
    } else if (phase === "holding") {
      timeout = setTimeout(() => setPhase("deleting"), 200);
    } else if (phase === "deleting") {
      if (text.length > 0) {
        timeout = setTimeout(() => setText(text.slice(0, -1)), deletingMs);
      } else {
        setIndex((i) => (i + 1) % strings.length);
        setPhase("typing");
      }
    }
    return () => clearTimeout(timeout);
  }, [text, phase, index, strings, typingMs, holdMs, deletingMs]);

  return text;
}

function SectionLabel({ children }) {
  return (
    <div className="flex items-center gap-3 mb-6">
      <span className="text-[#a855f7] font-mono text-sm">{"//"}</span>
      <h2 className="font-mono text-sm tracking-widest uppercase text-slate-300">
        {children}
      </h2>
      <div className="h-px flex-1 bg-gradient-to-r from-[#a855f7]/40 to-transparent" />
    </div>
  );
}

function Card({ children, className = "", delay = 0 }) {
  return (
    <motion.div
      initial={{ opacity: 0, y: 16 }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true, margin: "-40px" }}
      transition={{ duration: 0.45, delay }}
      className={`rounded-lg border border-white/10 bg-[#16213e]/80 backdrop-blur-sm ${className}`}
    >
      {children}
    </motion.div>
  );
}

// ---------- app ----------

export default function App() {
  const typedTitle = useTypingRotation(ROTATING_TITLES);
  const [quoteIndex, setQuoteIndex] = useState(0);
  const [visits] = useState(() => 1000 + Math.floor(Math.random() * 400));

  useEffect(() => {
    const id = setInterval(() => setQuoteIndex((i) => (i + 1) % QUOTES.length), 6000);
    return () => clearInterval(id);
  }, []);

  const maxCommits = useMemo(() => Math.max(...ACTIVITY.map((a) => a.commits)), []);

  return (
    <div className="min-h-screen w-full bg-[#1a1b2e] text-slate-200 font-sans selection:bg-[#a855f7]/30">
      <div className="mx-auto max-w-5xl px-5 sm:px-8 py-14 sm:py-20">

        {/* HERO */}
        <motion.section
          initial={{ opacity: 0, y: 24 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.6 }}
          className="mb-20 sm:mb-28"
        >
          <p className="font-mono text-xs text-[#a855f7] tracking-[0.3em] uppercase mb-4">
            @gabrieldev462
          </p>
          <h1 className="text-5xl sm:text-7xl font-bold tracking-tight text-white mb-4">
            Gabriel
          </h1>
          <div className="h-8 sm:h-9 flex items-center">
            <span className="font-mono text-base sm:text-lg text-slate-400">
              {typedTitle}
              <span className="inline-block w-[2px] h-5 bg-[#a855f7] ml-1 animate-pulse align-middle" />
            </span>
          </div>
          <p className="mt-6 max-w-xl text-sm sm:text-base text-slate-400 leading-relaxed">
            Rio Verde, GO — construindo integrações e automações de sistemas.
            Foco atual: middleware conectando Meta Ads, WhatsApp Cloud API e CRM
            imobiliário em produção.
          </p>
        </motion.section>

        {/* SOBRE MIM */}
        <section className="mb-20 sm:mb-24">
          <SectionLabel>sobre mim</SectionLabel>
          <Card className="overflow-hidden">
            <div className="flex items-center gap-2 px-4 py-3 border-b border-white/10 bg-black/20">
              <div className="flex gap-1.5">
                <span className="w-2.5 h-2.5 rounded-full bg-[#ff5f56]" />
                <span className="w-2.5 h-2.5 rounded-full bg-[#ffbd2e]" />
                <span className="w-2.5 h-2.5 rounded-full bg-[#27c93f]" />
              </div>
              <span className="font-mono text-xs text-slate-500 ml-2">gabriel.js</span>
            </div>
            <pre className="p-5 overflow-x-auto text-[13px] leading-relaxed">
              <code className="font-mono">
                {GABRIEL_OBJ.split("\n").map((line, i) => (
                  <div key={i} className="whitespace-pre">
                    <span className="text-slate-600 select-none inline-block w-6 text-right mr-4">
                      {i + 1}
                    </span>
                    <span
                      dangerouslySetInnerHTML={{
                        __html: line
                          .replace(/(".*?")/g, '<span style="color:#7dd3fc">$1</span>')
                          .replace(/(const|gabriel)/g, '<span style="color:#f472b6">$1</span>')
                          .replace(/^(\s*)([a-zA-Z]+)(:)/g, '$1<span style="color:#a855f7">$2</span>$3'),
                      }}
                    />
                  </div>
                ))}
              </code>
            </pre>
          </Card>
        </section>

        {/* TECNOLOGIAS */}
        <section className="mb-20 sm:mb-24">
          <SectionLabel>tecnologias & ferramentas</SectionLabel>
          <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
            {TECH_GROUPS.map((group, i) => (
              <Card key={group.label} delay={i * 0.06} className="p-5">
                <div className="flex items-center gap-2 mb-4 text-[#a855f7]">
                  <group.icon size={16} />
                  <span className="font-mono text-xs uppercase tracking-wider">
                    {group.label}
                  </span>
                </div>
                <div className="flex flex-wrap gap-2">
                  {group.items.map((item) => (
                    <span
                      key={item}
                      className="px-2.5 py-1 rounded text-xs font-mono bg-white/5 border border-white/10 text-slate-300"
                    >
                      {item}
                    </span>
                  ))}
                </div>
              </Card>
            ))}
          </div>
        </section>

        {/* STATS */}
        <section className="mb-20 sm:mb-24">
          <SectionLabel>stats do github</SectionLabel>
          <div className="grid grid-cols-2 sm:grid-cols-4 gap-4 mb-6">
            {STATS.map((stat, i) => (
              <Card key={stat.label} delay={i * 0.05} className="p-4 text-center">
                <stat.icon size={18} className="mx-auto mb-2 text-[#a855f7]" />
                <div className="text-2xl font-bold text-white font-mono">{stat.value}</div>
                <div className="text-[11px] text-slate-500 uppercase tracking-wider mt-1">
                  {stat.label}
                </div>
              </Card>
            ))}
          </div>

          <Card className="p-5">
            <p className="font-mono text-xs uppercase tracking-wider text-slate-500 mb-4">
              linguagens mais usadas
            </p>
            <div className="flex h-3 w-full rounded-full overflow-hidden mb-4">
              {LANG_BREAKDOWN.map((l) => (
                <div
                  key={l.name}
                  style={{ width: `${l.value}%`, backgroundColor: l.color }}
                  title={`${l.name} ${l.value}%`}
                />
              ))}
            </div>
            <div className="flex flex-wrap gap-x-6 gap-y-2">
              {LANG_BREAKDOWN.map((l) => (
                <div key={l.name} className="flex items-center gap-2 text-xs font-mono">
                  <span className="w-2.5 h-2.5 rounded-full" style={{ backgroundColor: l.color }} />
                  <span className="text-slate-300">{l.name}</span>
                  <span className="text-slate-500">{l.value}%</span>
                </div>
              ))}
            </div>
          </Card>
        </section>

        {/* STREAK */}
        <section className="mb-20 sm:mb-24">
          <SectionLabel>streak de contribuições</SectionLabel>
          <div className="grid grid-cols-1 sm:grid-cols-3 gap-4">
            <Card className="p-6 flex flex-col items-center justify-center text-center">
              <Flame size={20} className="text-[#a855f7] mb-2" />
              <div className="text-3xl font-bold text-white font-mono">47</div>
              <div className="text-[11px] text-slate-500 uppercase tracking-wider mt-1">
                sequência atual
              </div>
            </Card>
            <Card delay={0.06} className="p-6 flex flex-col items-center justify-center">
              <ResponsiveContainer width={110} height={110}>
                <RadialBarChart
                  innerRadius="70%"
                  outerRadius="100%"
                  data={[{ value: 53, fill: "#a855f7" }]}
                  startAngle={90}
                  endAngle={-270}
                >
                  <PolarAngleAxis type="number" domain={[0, 89]} angleAxisId={0} tick={false} />
                  <RadialBar background={{ fill: "#ffffff10" }} dataKey="value" cornerRadius={8} />
                </RadialBarChart>
              </ResponsiveContainer>
              <div className="text-2xl font-bold text-white font-mono -mt-16">89</div>
              <div className="text-[11px] text-slate-500 uppercase tracking-wider mt-16">
                melhor sequência
              </div>
            </Card>
            <Card delay={0.12} className="p-6 flex flex-col items-center justify-center text-center">
              <GitCommit size={20} className="text-[#a855f7] mb-2" />
              <div className="text-3xl font-bold text-white font-mono">1.204</div>
              <div className="text-[11px] text-slate-500 uppercase tracking-wider mt-1">
                total de contribuições
              </div>
            </Card>
          </div>
        </section>

        {/* TROPHIES */}
        <section className="mb-20 sm:mb-24">
          <SectionLabel>trophies</SectionLabel>
          <div className="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-6 gap-3">
            {TROPHIES.map((t, i) => (
              <motion.div
                key={t.label}
                initial={{ opacity: 0, scale: 0.9 }}
                whileInView={{ opacity: 1, scale: 1 }}
                viewport={{ once: true }}
                transition={{ duration: 0.35, delay: i * 0.05 }}
                whileHover={{ y: -3 }}
                className="rounded-lg border border-white/10 bg-gradient-to-b from-[#a855f7]/10 to-transparent p-4 text-center"
              >
                <Trophy size={18} className="mx-auto mb-2 text-[#a855f7]" />
                <div className="text-lg font-bold text-white font-mono">{t.value}</div>
                <div className="text-[10px] text-slate-500 uppercase tracking-wider mt-0.5">
                  {t.label}
                </div>
              </motion.div>
            ))}
          </div>
        </section>

        {/* PROJETOS */}
        <section className="mb-20 sm:mb-24">
          <SectionLabel>projetos em destaque</SectionLabel>
          <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
            {PROJECTS.map((p, i) => (
              <Card key={p.name} delay={i * 0.07} className="p-5 flex flex-col">
                <div className="flex items-start justify-between gap-2 mb-2">
                  <h3 className="font-mono text-sm font-bold text-white">{p.name}</h3>
                  <span className="shrink-0 px-2 py-0.5 rounded text-[10px] font-mono uppercase tracking-wide bg-[#a855f7]/15 text-[#c084fc] border border-[#a855f7]/25">
                    {p.status}
                  </span>
                </div>
                <p className="text-xs text-slate-400 leading-relaxed mb-4 flex-1">
                  {p.desc}
                </p>
                <div className="flex flex-wrap gap-1.5 mb-4">
                  {p.tags.map((tag) => (
                    <span
                      key={tag}
                      className="px-2 py-0.5 rounded text-[10px] font-mono bg-white/5 text-slate-400 border border-white/10"
                    >
                      {tag}
                    </span>
                  ))}
                </div>
                <button className="flex items-center gap-1.5 text-xs font-mono text-[#a855f7] hover:text-[#c084fc] transition-colors w-fit">
                  ver projeto <ExternalLink size={12} />
                </button>
              </Card>
            ))}
          </div>
        </section>

        {/* ATIVIDADE */}
        <section className="mb-20 sm:mb-24">
          <SectionLabel>atividade — últimos 12 meses</SectionLabel>
          <Card className="p-5">
            <div className="h-56">
              <ResponsiveContainer width="100%" height="100%">
                <AreaChart data={ACTIVITY} margin={{ top: 8, right: 8, left: -20, bottom: 0 }}>
                  <defs>
                    <linearGradient id="activityFill" x1="0" y1="0" x2="0" y2="1">
                      <stop offset="0%" stopColor="#a855f7" stopOpacity={0.35} />
                      <stop offset="100%" stopColor="#a855f7" stopOpacity={0} />
                    </linearGradient>
                  </defs>
                  <XAxis
                    dataKey="month"
                    tick={{ fill: "#64748b", fontSize: 11, fontFamily: "monospace" }}
                    axisLine={{ stroke: "#ffffff15" }}
                    tickLine={false}
                  />
                  <YAxis
                    domain={[0, maxCommits + 20]}
                    tick={{ fill: "#64748b", fontSize: 11, fontFamily: "monospace" }}
                    axisLine={false}
                    tickLine={false}
                  />
                  <Tooltip
                    contentStyle={{
                      background: "#16213e",
                      border: "1px solid #ffffff20",
                      borderRadius: 8,
                      fontFamily: "monospace",
                      fontSize: 12,
                    }}
                    labelStyle={{ color: "#e2e8f0" }}
                    itemStyle={{ color: "#a855f7" }}
                  />
                  <Area
                    type="monotone"
                    dataKey="commits"
                    stroke="#a855f7"
                    strokeWidth={2}
                    fill="url(#activityFill)"
                  />
                </AreaChart>
              </ResponsiveContainer>
            </div>
          </Card>
        </section>

        {/* CONECTAR */}
        <section className="mb-16 sm:mb-20">
          <SectionLabel>conecta</SectionLabel>
          <div className="flex flex-wrap gap-3">
            <a
              href="https://github.com/gabrieldev462"
              className="flex items-center gap-2 px-4 py-2.5 rounded-lg border border-white/10 bg-[#16213e] text-sm font-mono text-slate-300 hover:border-[#a855f7]/50 hover:text-white transition-colors"
            >
              <Github size={16} /> github
            </a>
            <a
              href="https://www.linkedin.com/in/gabriel-roberto-2581262bb/"
              className="flex items-center gap-2 px-4 py-2.5 rounded-lg border border-white/10 bg-[#16213e] text-sm font-mono text-slate-300 hover:border-[#a855f7]/50 hover:text-white transition-colors"
            >
              <Linkedin size={16} /> linkedin
            </a>
            <a
              href="mailto:gabrielrobertoribeiro27@gmail.com"
              className="flex items-center gap-2 px-4 py-2.5 rounded-lg border border-white/10 bg-[#16213e] text-sm font-mono text-slate-300 hover:border-[#a855f7]/50 hover:text-white transition-colors"
            >
              <Mail size={16} /> email
            </a>
            <a
              href="https://www.tiktok.com/@gabrielsouzag600"
              className="flex items-center gap-2 px-4 py-2.5 rounded-lg border border-white/10 bg-[#16213e] text-sm font-mono text-slate-300 hover:border-[#a855f7]/50 hover:text-white transition-colors"
            >
              <MessageSquareCode size={16} /> tiktok
            </a>
          </div>
        </section>

        {/* FRASE DO DIA */}
        <section className="mb-16 sm:mb-20">
          <Card className="p-6 sm:p-8 text-center border-[#a855f7]/20">
            <Cpu size={18} className="mx-auto mb-4 text-[#a855f7]/60" />
            <AnimatePresence mode="wait">
              <motion.div
                key={quoteIndex}
                initial={{ opacity: 0, y: 8 }}
                animate={{ opacity: 1, y: 0 }}
                exit={{ opacity: 0, y: -8 }}
                transition={{ duration: 0.4 }}
              >
                <p className="text-sm sm:text-base text-slate-300 italic max-w-md mx-auto leading-relaxed">
                  "{QUOTES[quoteIndex].text}"
                </p>
                <p className="mt-3 text-xs font-mono text-slate-500">
                  — {QUOTES[quoteIndex].author}
                </p>
              </motion.div>
            </AnimatePresence>
          </Card>
        </section>

        {/* FOOTER */}
        <footer className="text-center pt-8 border-t border-white/5">
          <p className="text-sm text-slate-400 mb-2">
            obrigado pela visita <span className="text-[#a855f7]">★</span>
          </p>
          <p className="font-mono text-[11px] text-slate-600">
            {visits.toLocaleString("pt-BR")} visitas ao perfil
          </p>
        </footer>
      </div>
    </div>
  );
}
