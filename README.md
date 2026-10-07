<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>EstudeMap</title>
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
<style>
body{background:#111827;color:white;font-family:'Segoe UI',Tahoma,Geneva,Verdana,sans-serif}
::-webkit-scrollbar{width:8px}::-webkit-scrollbar-track{background:#1f2937}::-webkit-scrollbar-thumb{background:#4b5563;border-radius:4px}
button:disabled{opacity:.5;cursor:not-allowed}
</style>
</head>
<body>
<div id="root"></div>
<script type="text/babel">
const {useState,useEffect,useMemo}=React;
const STORAGE_KEY="gps-enem-2027-v2";

const MAPA_MATERIAS=[
{id:1,nome:"Matemática",icone:"🧮",prioridade:5,fases:[
{nome:"Fase 1 — Base",topicos:["Operações básicas","Frações","Razão e proporção","Regra de três","Porcentagem","Números decimais","Potenciação","Radiciação","Notação científica","MMC e MDC","Expressões numéricas","Produtos notáveis","Fatoração"]},
{nome:"Fase 2 — Álgebra",topicos:["Equações de 1º grau","Equações de 2º grau","Sistemas de equações","Inequações","Função","Função afim","Função quadrática","Função exponencial","Função logarítmica"]},
{nome:"Fase 3 — Geometria",topicos:["Ângulos","Triângulos","Teorema de Pitágoras","Semelhança","Polígonos","Circunferência","Área de figuras planas","Perímetro","Geometria espacial","Prismas","Cilindros","Pirâmides","Cones","Esferas","Volume"]},
{nome:"Fase 4 — ENEM",topicos:["Estatística","Média","Mediana","Moda","Variância e desvio padrão","Probabilidade","Análise combinatória","PA e PG","Matemática financeira","Juros simples","Juros compostos","Escalas","Grandezas e unidades","Gráficos e tabelas"]}]},
{id:2,nome:"Português",icone:"📖",prioridade:5,fases:[
{nome:"Interpretação",topicos:["Interpretação de texto","Tipos de texto","Gêneros textuais","Funções da linguagem","Denotação e conotação","Figuras de linguagem","Intertextualidade","Ironia","Ambiguidade","Coesão","Coerência"]},
{nome:"Gramática",topicos:["Classes gramaticais","Substantivo","Adjetivo","Pronome","Verbo","Advérbio","Preposição","Conjunção","Artigo","Numeral","Sintaxe","Sujeito","Predicado","Complementos","Período simples e composto","Coordenação","Subordinação","Concordância","Regência","Crase","Pontuação"]},
{nome:"Literatura",topicos:["Quinhentismo","Barroco","Arcadismo","Romantismo","Realismo","Naturalismo","Parnasianismo","Simbolismo","Pré-modernismo","Modernismo","Literatura contemporânea"]}]},
{id:3,nome:"Biologia",icone:"🧬",prioridade:4,fases:[
{nome:"Base",topicos:["Citologia","Organelas","Membrana plasmática","Transporte celular","Metabolismo","Respiração celular","Fotossíntese","Divisão celular"]},
{nome:"Genética",topicos:["DNA e RNA","Síntese proteica","Genética mendeliana","Hereditariedade","Probabilidade genética","Mutação","Biotecnologia"]},
{nome:"Evolução e Ecologia",topicos:["Lamarckismo","Darwinismo","Seleção natural","Especiação","Evolução humana","Cadeias alimentares","Teias alimentares","Fluxo de energia","Ciclos biogeoquímicos","Relações ecológicas","Populações","Comunidades","Biomas","Impactos ambientais","Sustentabilidade"]},
{nome:"Corpo Humano e Outros",topicos:["Sistema digestório","Sistema respiratório","Sistema circulatório","Sistema nervoso","Sistema endócrino","Sistema reprodutor","Imunologia","Doenças","Zoologia","Botânica","Microbiologia","Parasitologia"]}]},
{id:4,nome:"Química",icone:"⚗️",prioridade:4,fases:[
{nome:"Base",topicos:["Matéria","Estados físicos","Propriedades da matéria","Separação de misturas","Átomos","Elementos químicos","Tabela periódica","Ligações químicas"]},
{nome:"Química geral",topicos:["Funções inorgânicas","Ácidos","Bases","Sais","Óxidos","Reações químicas","Balanceamento","Mol","Massa molar","Estequiometria"]},
{nome:"Físico-química",topicos:["Soluções","Concentração","Diluição","Termoquímica","Cinética química","Equilíbrio químico","pH","Eletroquímica","Pilhas","Eletrólise"]},
{nome:"Orgânica",topicos:["Carbono","Hidrocarbonetos","Funções orgânicas","Isomeria","Reações orgânicas","Polímeros","Petróleo","Biocombustíveis"]}]},
{id:5,nome:"Física",icone:"⚡",prioridade:4,fases:[
{nome:"Mecânica",topicos:["Grandezas físicas","Unidades","Cinemática","Velocidade","Aceleração","Movimento uniforme","Movimento uniformemente variado","Gráficos","Leis de Newton","Força","Atrito","Trabalho","Energia","Potência","Impulso","Quantidade de movimento"]},
{nome:"Gravitação e Termologia",topicos:["Gravidade","Leis de Kepler","Gravitação universal","Temperatura","Calor","Mudança de estado","Dilatação","Termodinâmica"]},
{nome:"Ondas e Óptica",topicos:["Ondas","Frequência","Comprimento de onda","Som","Efeito Doppler","Reflexão","Refração","Espelhos","Lentes"]},
{nome:"Eletricidade e Eletromagnetismo",topicos:["Carga elétrica","Corrente","Tensão","Resistência","Lei de Ohm","Circuitos","Potência elétrica","Energia elétrica","Eletromagnetismo"]}]},
{id:6,nome:"Geografia",icone:"🌎",prioridade:4,fases:[
{nome:"Base",topicos:["Cartografia","Escalas","Coordenadas geográficas","Fusos horários","Mapas","Relevo","Clima","Vegetação","Hidrografia"]},
{nome:"Brasil",topicos:["Formação territorial","Regiões brasileiras","Urbanização","Industrialização","Agricultura","Questão agrária","População","Migrações","Energia","Recursos naturais"]},
{nome:"Geopolítica e Meio ambiente",topicos:["Globalização","Capitalismo","Socialismo","Guerra Fria","Nova ordem mundial","Blocos econômicos","Conflitos internacionais","Mudanças climáticas","Desmatamento","Poluição","Desenvolvimento sustentável","Fontes de energia"]}]},
{id:7,nome:"História",icone:"🏛️",prioridade:4,fases:[
{nome:"História Geral",topicos:["Antiguidade","Grécia","Roma","Idade Média","Feudalismo","Igreja","Renascimento","Reforma Protestante","Absolutismo","Mercantilismo","Revolução Inglesa","Iluminismo","Revolução Francesa","Revolução Industrial"]},
{nome:"História do Brasil",topicos:["Brasil Colonial","Escravidão","Economia colonial","Independência","Primeiro Reinado","Período Regencial","Segundo Reinado","Abolição","República Velha","Era Vargas","Estado Novo","Ditadura Militar","Redemocratização"]},
{nome:"Brasil contemporâneo e Século XX",topicos:["Primeira Guerra","Revolução Russa","Crise de 1929","Segunda Guerra","Guerra Fria","Descolonização","Globalização"]}]},
{id:8,nome:"Filosofia",icone:"🧠",prioridade:3,fases:[
{nome:"Antiga e Medieval",topicos:["Pré-socráticos","Sócrates","Platão","Aristóteles","Filosofia medieval","Santo Agostinho","São Tomás de Aquino"]},
{nome:"Moderna e Contemporânea",topicos:["Renascimento","Maquiavel","Hobbes","Locke","Rousseau","Kant","Hegel","Marx","Nietzsche","Existencialismo","Filosofia contemporânea"]}]},
{id:9,nome:"Sociologia",icone:"👥",prioridade:3,fases:[
{nome:"Clássicos e Conceitos",topicos:["Surgimento da Sociologia","Auguste Comte","Émile Durkheim","Karl Marx","Max Weber","Cultura","Etnocentrismo","Socialização","Instituições sociais"]},
{nome:"Temas Contemporâneos",topicos:["Trabalho","Classes sociais","Desigualdade","Poder","Estado","Cidadania","Movimentos sociais","Globalização","Indústria cultural","Mídia","Identidade","Questões sociais contemporâneas"]}]}
];

const TOPICOS=MAPA_MATERIAS.flatMap(m=>m.fases.flatMap(f=>f.topicos.map(t=>({materia:m.nome,fase:f.nome,topico:t}))));
const MATERIAS=MAPA_MATERIAS.map(m=>m.nome);

const DEFAULT_STATE={
cronograma:[],topicoStatus:{},redacoes:[],flashcards:[
{id:"f1",materia:"Matemática",pergunta:"O que é um número primo?",resposta:"Um número maior que 1 que só é divisível por 1 e por ele mesmo."},
{id:"f2",materia:"Biologia",pergunta:"Qual a função da mitocôndria?",resposta:"Participa da respiração celular e da produção de ATP."},
{id:"f3",materia:"Física",pergunta:"Qual é a fórmula da força na 2ª Lei de Newton?",resposta:"F = m · a."}
],revisoes:[],config:{minutos:90,dias:6,enemNome:"ENEM 2027",enemData:""}};
function loadState(){
try{const saved=JSON.parse(localStorage.getItem(STORAGE_KEY)||"{}");delete saved.questoes;delete saved.diagnostico;delete saved.simulados;const merged={...DEFAULT_STATE,...saved};merged.config={...DEFAULT_STATE.config,...(saved.config||{})};return merged}catch(e){return DEFAULT_STATE}
}
function saveState(s){try{localStorage.setItem(STORAGE_KEY,JSON.stringify(s))}catch(e){}}
function uid(){return Date.now().toString(36)+Math.random().toString(36).slice(2,7)}
function hoje(){const d=new Date();const y=d.getFullYear();const m=String(d.getMonth()+1).padStart(2,"0");const day=String(d.getDate()).padStart(2,"0");return `${y}-${m}-${day}`}
function addDays(date,n){const [y,m,d]=date.split("-").map(Number);const x=new Date(y,m-1,d);x.setDate(x.getDate()+n);return `${x.getFullYear()}-${String(x.getMonth()+1).padStart(2,"0")}-${String(x.getDate()).padStart(2,"0")}`}
function formatDate(s){return new Date(s+"T12:00:00").toLocaleDateString("pt-BR")}
function allTopicsBySubject(nome){return TOPICOS.filter(t=>t.materia===nome)}
function topicKey(m,t){return m+"::"+t}
function progressoMateria(state,nome){
const ts=allTopicsBySubject(nome); if(!ts.length)return 0;
const status=state.topicoStatus||{};const done=ts.filter(x=>status[topicKey(x.materia,x.topico)]==="concluido").length;
return Math.round(done/ts.length*100);
}
function totalTopics(){return TOPICOS.length}
function diasSemana(n){return Array.from({length:n},(_,i)=>i)}
function shuffle(arr){return [...arr].sort(()=>Math.random()-.5)}

function Sidebar({aba,nav}){
return <nav className="w-64 bg-gray-800 p-5 flex flex-col gap-2 border-r border-gray-700 flex-shrink-0">
<div className="mb-6"><h1 className="text-2xl font-bold text-blue-400">EstudeMap</h1><p className="text-sm text-gray-400">Menos caos. Mais foco. Tudo em um só lugar.</p></div>
{[
["dashboard","📊 Início"],["cronograma","📅 Meu Cronograma"],["mapa","🗺️ Mapa de matérias"],["flashcards","🃏 Revisão espaçada"],["redacao","✍️ Redação"],["simulados","🎯 Simulados externos"],["config","⚙️ Meu plano"]
].map(([id,label])=><button key={id} onClick={()=>nav(id)} className={`p-3 rounded-lg text-left font-semibold ${aba===id?"bg-blue-600":"hover:bg-gray-700"}`}>{label}</button>)}
<div className="mt-auto text-xs text-gray-500">Dados salvos automaticamente neste navegador.</div>
</nav>
}

function Dashboard({state,progressoGeral,totalConcluidos,pendentesRevisao,nav}){
return <div>
<div className="flex flex-wrap justify-between gap-4 mb-6"><div><h2 className="text-3xl font-bold">Seu painel</h2><p className="text-gray-400">Veja exatamente onde você está no caminho até o ENEM.</p></div></div>
<div className="grid md:grid-cols-3 gap-5 mb-8">
<div className="bg-gray-800 p-5 rounded-xl border border-gray-700"><p className="text-gray-400">Progresso do conteúdo</p><p className="text-4xl font-bold mt-2">{progressoGeral}%</p><div className="h-3 bg-gray-700 rounded-full mt-3"><div className="h-3 bg-blue-500 rounded-full" style={{width:`${progressoGeral}%`}}/></div><p className="text-xs text-gray-500 mt-2">{totalConcluidos} de {totalTopics()} tópicos</p></div>

<div className="bg-gray-800 p-5 rounded-xl border border-gray-700"><p className="text-gray-400">Revisões de flashcards vencidas</p><p className="text-4xl font-bold mt-2">{pendentesRevisao}</p><button onClick={()=>nav("flashcards")} className="text-blue-400 mt-2">Revisar agora →</button></div>
</div>
<h3 className="text-xl font-bold mb-4">Progresso por matéria</h3>
<div className="grid md:grid-cols-2 lg:grid-cols-3 gap-4">
{MAPA_MATERIAS.map(m=><div key={m.id} className="bg-gray-800 p-5 rounded-xl border border-gray-700"><div className="flex justify-between"><b>{m.icone} {m.nome}</b><span className="text-yellow-400">{'⭐'.repeat(m.prioridade)}</span></div><div className="h-3 bg-gray-700 rounded-full mt-4"><div className="h-3 bg-blue-500 rounded-full" style={{width:`${progressoMateria(state,m.nome)}%`}}/></div><div className="flex justify-between text-sm mt-2"><span>{progressoMateria(state,m.nome)}%</span><span className="text-gray-500">{allTopicsBySubject(m.nome).length} tópicos</span></div></div>)}
</div>
</div>
}

function Cronograma({state,adicionarTarefaManual,removerTarefa,toggleTask,salvarNotasTarefa}){
const [data,setData]=useState(hoje());
const [materia,setMateria]=useState(MATERIAS[0]);
const [topico,setTopico]=useState(allTopicsBySubject(MATERIAS[0])[0]?.topico||"");
const [duracao,setDuracao]=useState(30);
const [estudoAberto,setEstudoAberto]=useState(null);
const [notaTemp,setNotaTemp]=useState("");

useEffect(()=>{setTopico(allTopicsBySubject(materia)[0]?.topico||"")},[materia]);
const topics=allTopicsBySubject(materia);
const grouped=Object.entries(state.cronograma.reduce((a,x)=>{(a[x.data]??=[]).push(x);return a},{})).sort(([a],[b])=>a.localeCompare(b));
function adicionar(){adicionarTarefaManual(data,materia,topico,duracao)}
function abrirEstudo(t){setEstudoAberto(t);setNotaTemp(t.notas||"")}
function fecharEstudo(){setEstudoAberto(null);setNotaTemp("")}
function salvarEFechar(){if(!estudoAberto)return;salvarNotasTarefa(estudoAberto.id,notaTemp);fecharEstudo()}
return <div>
<div className="mb-6"><h2 className="text-3xl font-bold">Meu Cronograma</h2><p className="text-gray-400">Você decide o que estudar e quando. Nada é gerado automaticamente.</p></div>
<div className="bg-gray-800 p-6 rounded-xl border border-gray-700 mb-8">
<h3 className="text-xl font-bold mb-4">➕ Adicionar estudo</h3>
<div className="grid md:grid-cols-4 gap-4 items-end">
<div><label className="block text-sm text-gray-400 mb-1">Data</label><input type="date" value={data} onChange={e=>setData(e.target.value)} className="w-full bg-gray-700 p-3 rounded-lg"/></div>
<div><label className="block text-sm text-gray-400 mb-1">Matéria</label><select value={materia} onChange={e=>setMateria(e.target.value)} className="w-full bg-gray-700 p-3 rounded-lg">{MATERIAS.map(m=><option key={m}>{m}</option>)}</select></div>
<div><label className="block text-sm text-gray-400 mb-1">Tópico</label><select value={topico} onChange={e=>setTopico(e.target.value)} className="w-full bg-gray-700 p-3 rounded-lg">{topics.map(t=><option key={t.topico}>{t.topico}</option>)}</select></div>
<div><label className="block text-sm text-gray-400 mb-1">Tempo</label><select value={duracao} onChange={e=>setDuracao(Number(e.target.value))} className="w-full bg-gray-700 p-3 rounded-lg">{[30,45,60,90].map(x=><option key={x} value={x}>{x} min</option>)}</select></div>
</div>
<button onClick={adicionar} className="mt-5 bg-blue-600 hover:bg-blue-500 px-6 py-3 rounded-lg font-bold">+ Adicionar ao cronograma</button>
</div>
<div className="mb-5 flex justify-between items-center"><div><h3 className="text-xl font-bold">Meus estudos planejados</h3><p className="text-sm text-gray-500">Clique em qualquer estudo para abrir suas anotações do dia.</p></div><span className="text-gray-400">{state.cronograma.length} estudo(s)</span></div>
{grouped.length===0?<div className="bg-gray-800 p-8 rounded-xl text-center text-gray-400">Você ainda não adicionou nenhum estudo ao cronograma.</div>:<div className="space-y-6">{grouped.map(([dia,tasks])=><div key={dia}><h3 className="text-lg font-bold mb-2 text-blue-400">{formatDate(dia)}</h3><div className="space-y-2">{tasks.map(t=><div key={t.id} className={`p-4 rounded-xl border flex items-center justify-between gap-4 ${t.concluido?"bg-green-900/20 border-green-700":"bg-gray-800 border-gray-700"}`}>
<button onClick={()=>abrirEstudo(t)} className="flex-1 text-left hover:bg-gray-700/30 rounded-lg p-1 -m-1 transition"><div className="flex gap-2 items-center"><b>{t.materia}</b><span className="text-xs text-gray-500">• {t.duracao} min</span></div><p className="text-gray-300">{t.topico}</p><p className="text-xs mt-2 text-blue-400">{t.notas?.trim()?"📝 Anotações salvas — clique para abrir":"📝 Adicionar anotações do estudo"}</p></button>
<div className="flex gap-2"><button onClick={()=>toggleTask(t.id)} className={`px-4 py-2 rounded-lg font-bold ${t.concluido?"bg-green-600":"bg-gray-700 hover:bg-gray-600"}`}>{t.concluido?"✓ Concluído":"Marcar como feito"}</button><button onClick={()=>removerTarefa(t.id)} className="px-4 py-2 rounded-lg bg-red-900/50 hover:bg-red-600 text-red-200" title="Remover">🗑️</button></div>
</div>)}</div></div>)}</div>}
{estudoAberto&&<div className="fixed inset-0 z-50 bg-black/70 flex items-center justify-center p-4" onMouseDown={e=>{if(e.target===e.currentTarget)fecharEstudo()}}>
<div className="w-full max-w-2xl bg-gray-800 rounded-2xl border border-gray-700 shadow-2xl overflow-hidden">
<div className="p-5 border-b border-gray-700 flex justify-between items-start gap-4"><div><p className="text-sm text-blue-400 font-semibold">{formatDate(estudoAberto.data)} • {estudoAberto.duracao} min</p><h3 className="text-2xl font-bold mt-1">{estudoAberto.materia}</h3><p className="text-gray-300 mt-1">{estudoAberto.topico}</p></div><button onClick={fecharEstudo} className="text-gray-400 hover:text-white text-2xl" title="Fechar">×</button></div>
<div className="p-5"><label className="block font-bold mb-2">📝 O que eu estudei hoje?</label><p className="text-sm text-gray-400 mb-3">Escreva com suas próprias palavras o que você aprendeu, os pontos importantes, exemplos, dúvidas ou aquilo que precisa revisar.</p>
<textarea autoFocus value={notaTemp} onChange={e=>setNotaTemp(e.target.value)} placeholder="Ex.: Hoje eu entendi que porcentagem representa uma parte de um todo..." className="w-full min-h-[300px] bg-gray-900 border border-gray-700 rounded-xl p-4 resize-y leading-relaxed focus:outline-none focus:border-blue-500"/>
<div className="flex justify-between items-center mt-4 gap-3"><span className="text-xs text-gray-500">Suas anotações ficam salvas junto deste estudo.</span><div className="flex gap-2"><button onClick={fecharEstudo} className="px-4 py-2 rounded-lg bg-gray-700 hover:bg-gray-600">Cancelar</button><button onClick={salvarEFechar} className="px-5 py-2 rounded-lg bg-blue-600 hover:bg-blue-500 font-bold">Salvar anotações</button></div></div>
</div></div></div>}
</div>
}

function Mapa({state,fase,setFase,setTopicStatus}){
return <div><h2 className="text-3xl font-bold mb-2">Mapa de matérias</h2><p className="text-gray-400 mb-6">Marque cada tópico conforme você domina o conteúdo. O progresso é calculado pelos tópicos reais.</p><div className="grid lg:grid-cols-3 gap-5">{MAPA_MATERIAS.map(m=><div className="bg-gray-800 p-5 rounded-xl border border-gray-700" key={m.id}><h3 className="text-xl font-bold mb-3">{m.icone} {m.nome} — {progressoMateria(state,m.nome)}%</h3>{m.fases.map(f=>{const id=m.nome+f.nome;return <div key={id} className="mb-2 bg-gray-700 rounded-lg overflow-hidden"><button className="w-full p-3 text-left font-semibold flex justify-between" onClick={()=>setFase(fase===id?null:id)}>{f.nome}<span>{fase===id?"▼":"▶"}</span></button>{fase===id&&<div className="p-3 bg-gray-900 space-y-2">{f.topicos.map(t=>{const st=state.topicoStatus[topicKey(m.nome,t)]||"nao_iniciado";return <button key={t} onClick={()=>setTopicStatus(m.nome,t,st==="concluido"?"nao_iniciado":"concluido")} className="w-full text-left p-2 rounded hover:bg-gray-800 flex justify-between gap-2"><span>{t}</span><span className={st==="concluido"?"text-green-400":"text-gray-500"}>{st==="concluido"?"✓":"○"}</span></button>})}</div>}</div>})}</div>)}</div></div>
}

function Flashcards({state,cardIndex,setCardIndex,virado,setVirado,novoCard,setNovoCard,adicionarCard,avaliarCard}){
const card=state.flashcards[cardIndex];const revisoesHoje=state.revisoes.filter(r=>r.flashcardId&&r.proxima<=hoje());
return <div><div className="flex justify-between mb-6"><div><h2 className="text-3xl font-bold">Revisão espaçada</h2><p className="text-gray-400">Intervalos: 1 → 3 → 7 → 15 → 30 → 60 dias.</p></div><span className="text-blue-400">{revisoesHoje.length} revisão(ões) vencida(s)</span></div>
<div className="grid lg:grid-cols-3 gap-6"><div className="bg-gray-800 p-5 rounded-xl border border-gray-700"><h3 className="font-bold mb-4">Criar flashcard</h3><select value={novoCard.materia} onChange={e=>setNovoCard({...novoCard,materia:e.target.value})} className="w-full bg-gray-700 p-3 rounded mb-3">{MATERIAS.map(m=><option key={m}>{m}</option>)}</select><textarea value={novoCard.pergunta} onChange={e=>setNovoCard({...novoCard,pergunta:e.target.value})} className="w-full bg-gray-700 p-3 rounded mb-3" placeholder="Pergunta"/><textarea value={novoCard.resposta} onChange={e=>setNovoCard({...novoCard,resposta:e.target.value})} className="w-full bg-gray-700 p-3 rounded mb-3" placeholder="Resposta"/><button onClick={adicionarCard} className="w-full bg-blue-600 p-3 rounded font-bold">Adicionar</button></div>
<div className="lg:col-span-2 bg-gray-800 p-7 rounded-xl border border-gray-700 text-center"><p className="text-gray-400 mb-4">{card?.materia} • Card {cardIndex+1}/{state.flashcards.length}</p>{card?<><div onClick={()=>setVirado(!virado)} className={`min-h-64 ${virado?"bg-green-600 text-white":"bg-white text-gray-900"} rounded-2xl flex items-center justify-center p-8 cursor-pointer`}><div><p className="text-2xl font-bold">{virado?card.resposta:card.pergunta}</p><p className={`text-sm mt-5 ${virado?"text-green-100":"text-gray-500"}`}>Clique para virar</p></div></div><div className="flex justify-center gap-4 mt-5"><button disabled={!virado} onClick={()=>avaliarCard("errou")} className="bg-red-600 px-6 py-3 rounded-lg font-bold">❌ Errei</button><button disabled={!virado} onClick={()=>avaliarCard("acertou")} className="bg-green-600 px-6 py-3 rounded-lg font-bold">✅ Acertei</button></div></>:<p>Adicione um card.</p>}</div></div></div>
}

function Redacao({state,salvarRedacao,excluirRedacao}){
const vazio={titulo:"",texto:"",c1:"",c2:"",c3:"",c4:"",c5:""};
const [form,setForm]=useState(vazio);const [editandoId,setEditandoId]=useState(null);
function update(campo,valor){setForm(prev=>({...prev,[campo]:valor}))}
function carregar(r){setEditandoId(r.id);setForm({titulo:r.titulo||"",texto:r.texto||"",c1:String(r.notas?.[0]??0),c2:String(r.notas?.[1]??0),c3:String(r.notas?.[2]??0),c4:String(r.notas?.[3]??0),c5:String(r.notas?.[4]??0)})}
function limpar(){setEditandoId(null);setForm(vazio)}
function salvar(){if(salvarRedacao(form,editandoId))limpar()}
const words=form.texto.trim()?form.texto.trim().split(/\s+/).length:0;const total=[1,2,3,4,5].reduce((a,i)=>a+(Number(form["c"+i])||0),0);
const competencias=[
{numero:1,titulo:"Domínio da norma padrão",descricao:"Avalia ortografia, gramática, concordância, regência, pontuação e uso adequado da língua portuguesa."},
{numero:2,titulo:"Compreensão da proposta",descricao:"Avalia se o tema foi compreendido e desenvolvido em um texto dissertativo-argumentativo, com repertório pertinente."},
{numero:3,titulo:"Seleção e organização dos argumentos",descricao:"Avalia a seleção, relação, organização e interpretação das informações para defender seu ponto de vista."},
{numero:4,titulo:"Mecanismos linguísticos",descricao:"Avalia a coesão e o uso de recursos linguísticos para conectar ideias, frases e parágrafos com lógica."},
{numero:5,titulo:"Proposta de intervenção",descricao:"Avalia uma proposta de intervenção para o problema abordado, com detalhamento e respeito aos direitos humanos."}
];
return <div><div className="flex justify-between items-center mb-2"><div><h2 className="text-3xl font-bold">{editandoId?"Editando redação ✏️":"Laboratório de Redação"}</h2><p className="text-gray-400 mb-6">Registre a redação e as cinco competências em escala de 0 a 200.</p></div>{editandoId&&<button onClick={limpar} className="bg-gray-700 hover:bg-gray-600 px-4 py-2 rounded-lg">Cancelar edição</button>}</div>
<div className="bg-gray-800 p-5 rounded-xl border border-gray-700 mb-6"><div className="flex flex-wrap items-end justify-between gap-3 mb-4"><div><h3 className="text-xl font-bold">📚 As 5 competências do ENEM</h3><p className="text-sm text-gray-400 mt-1">Use como checklist para avaliar sua redação.</p></div><span className="text-xs text-blue-300 bg-blue-600/20 px-3 py-1 rounded-full">Até 200 pontos cada</span></div><div className="grid md:grid-cols-2 xl:grid-cols-5 gap-3">{competencias.map(c=><div key={c.numero} className="bg-gray-900 rounded-lg p-4 border border-gray-700"><div className="flex items-center gap-2 mb-2"><span className="w-8 h-8 rounded-full bg-blue-600 flex items-center justify-center font-bold">{c.numero}</span><h4 className="font-bold text-sm">C{c.numero}</h4></div><p className="font-semibold text-sm leading-snug">{c.titulo}</p><p className="text-xs text-gray-400 mt-2 leading-relaxed">{c.descricao}</p></div>)}</div></div>
<div className="grid lg:grid-cols-3 gap-6"><div className="lg:col-span-2"><input value={form.titulo} onChange={e=>update("titulo",e.target.value)} className="w-full bg-gray-800 p-4 rounded-xl mb-3" placeholder="Tema da redação"/><textarea value={form.texto} onChange={e=>update("texto",e.target.value)} className="w-full bg-gray-800 p-5 rounded-xl min-h-[430px] resize-none leading-relaxed" placeholder="Escreva sua redação..." spellCheck="true"/><div className="flex justify-between mt-3 text-gray-400"><span>{words} palavras</span><button onClick={salvar} className="bg-blue-600 px-6 py-3 rounded-lg font-bold">{editandoId?"Atualizar redação":"Salvar redação"}</button></div></div>
<div className="bg-gray-800 p-5 rounded-xl"><h3 className="font-bold text-xl mb-4">Notas</h3>{[1,2,3,4,5].map(i=><label key={i} className="block text-sm text-gray-300 mb-3">Competência {i}<input type="number" min="0" max="200" step="40" value={form["c"+i]} onChange={e=>update("c"+i,e.target.value)} className="w-full bg-gray-700 p-3 rounded mt-1" placeholder="0 a 200"/></label>)}<p className="mt-4 text-gray-400">Total: <b className="text-white">{total}/1000</b></p><p className="mt-2 text-xs text-gray-500">Valores permitidos: 0, 40, 80, 120, 160 ou 200.</p></div></div>
<div className="mt-8"><h3 className="text-xl font-bold mb-3">Minhas redações</h3><div className="space-y-3">{state.redacoes.length===0?<p className="text-gray-500">Nenhuma redação salva ainda.</p>:state.redacoes.map(r=><div key={r.id} onClick={()=>carregar(r)} className={`bg-gray-800 p-5 rounded-xl border flex items-center justify-between gap-4 cursor-pointer ${editandoId===r.id?"border-amber-500":"border-gray-700 hover:border-gray-500"}`}><div className="min-w-0"><b className="block truncate">{r.titulo}</b><p className="text-sm text-gray-500">{formatDate(r.data)} • {r.texto.split(/\s+/).filter(Boolean).length} palavras</p></div><div className="flex items-center gap-3 flex-shrink-0"><b className="text-yellow-400">{r.total}/1000</b><button onClick={e=>{e.stopPropagation();excluirRedacao(r.id)}} className="p-2 bg-red-900/40 hover:bg-red-600 text-red-200 rounded-lg" title="Excluir redação">🗑️</button></div></div>)}</div></div>
</div>}

function SimuladosExternos(){
const links=[
{nome:"🏛️ Provas oficiais do ENEM",descricao:"Provas e gabaritos oficiais disponibilizados pelo INEP, com várias edições anteriores.",badge:"Oficial",url:"https://www.gov.br/inep/pt-br/areas-de-atuacao/avaliacao-e-exames-educacionais/enem/provas-e-gabaritos",botao:"Abrir provas e gabaritos →"},
{nome:"📚 Quizlet — Simulado ENEM",descricao:"Simulados completos, separados por dia do ENEM e também por matéria. O acesso gratuito pode ter limitações conforme a conta e o recurso.",badge:"Gratuito*",url:"https://quizlet.com/br/content/simulado-enem",botao:"Abrir simulados →"},
{nome:"📝 Brasil Escola",descricao:"Acesse a área de vestibular para encontrar materiais e simulados online de preparação para o ENEM.",badge:"Gratuito",url:"https://vestibular.brasilescola.uol.com.br/enem",botao:"Acessar →"}
];
return <div className="max-w-6xl mx-auto"><div className="mb-8"><h2 className="text-3xl font-bold mb-2">🎯 Simulados Externos</h2><p className="text-gray-400">O GPS não repete simulados internos. Aqui você encontra opções externas para fazer provas novas e provas oficiais.</p></div><div className="grid md:grid-cols-2 gap-6">{links.map(x=><div key={x.url} className="bg-gray-800 p-6 rounded-xl border border-gray-700 shadow-lg"><div className="flex justify-between gap-3 items-start"><div><h3 className="text-xl font-bold">{x.nome}</h3><p className="text-sm text-gray-400 mt-2">{x.descricao}</p></div><span className="text-xs bg-blue-600/20 text-blue-300 px-3 py-1 rounded-full whitespace-nowrap">{x.badge}</span></div><a href={x.url} target="_blank" rel="noopener noreferrer" className="inline-block mt-5 bg-blue-600 hover:bg-blue-500 px-5 py-3 rounded-lg font-bold">{x.botao}</a></div>)}<div className="bg-gray-800 p-6 rounded-xl border border-gray-700"><h3 className="text-xl font-bold mb-4">💡 Como usar</h3><ol className="list-decimal list-inside text-gray-300 text-sm space-y-3"><li>Escolha uma prova ou simulado externo.</li><li>Faça sem consultar o gabarito e controle o tempo.</li><li>Anote seus erros e dificuldades no seu cronograma ou nos seus materiais de estudo.</li><li>Volte ao <b>Mapa de matérias</b> para revisar os conteúdos em que teve dificuldade.</li></ol></div></div><p className="text-xs text-gray-500 mt-6">Os sites externos podem alterar disponibilidade, regras de acesso e conteúdo sem aviso. O GPS apenas direciona você para eles.</p></div>
}

function Config({config,setConfig,updateState,setToast}){
const [agora,setAgora]=useState(()=>new Date());
useEffect(()=>{const timer=setInterval(()=>setAgora(new Date()),1000);return()=>clearInterval(timer)},[]);
function calcularContagem(){
if(!config.enemData)return null;
const [ano,mes,dia]=config.enemData.split("-").map(Number);
if(!ano||!mes||!dia)return null;
const alvo=new Date(ano,mes-1,dia,23,59,59,999);
const ms=alvo.getTime()-agora.getTime();
if(ms<=0)return {encerrado:true};
const totalSeg=Math.floor(ms/1000);
const dias=Math.floor(totalSeg/86400);
const horas=Math.floor(totalSeg%86400/3600);
const minutos=Math.floor(totalSeg%3600/60);
const segundos=totalSeg%60;
return {encerrado:false,dias,horas,minutos,segundos};
}
const contagem=calcularContagem();
return <div className="max-w-3xl"><h2 className="text-3xl font-bold mb-2">Meu plano</h2><p className="text-gray-400 mb-6">Defina sua meta de ENEM e suas referências de estudo. O cronograma continua sendo montado manualmente por você.</p>
<div className="bg-gray-800 p-6 rounded-xl border border-gray-700 mb-6">
<h3 className="text-xl font-bold mb-4">🎯 Meu ENEM</h3>
<div className="grid md:grid-cols-2 gap-4">
<label className="block">Qual ENEM você pretende fazer?<input value={config.enemNome||""} onChange={e=>setConfig({...config,enemNome:e.target.value})} className="w-full bg-gray-700 p-3 rounded mt-2" placeholder="Ex.: ENEM 2027"/></label>
<label className="block">Data da prova<input type="date" value={config.enemData||""} onChange={e=>setConfig({...config,enemData:e.target.value})} className="w-full bg-gray-700 p-3 rounded mt-2"/></label>
</div>
{contagem?.encerrado?<div className="mt-5 bg-red-900/20 border border-red-800 rounded-xl p-5"><p className="text-red-300 font-bold">⏰ A data definida para {config.enemNome||"seu ENEM"} já passou.</p><p className="text-sm text-gray-400 mt-1">Escolha uma nova data para iniciar uma nova contagem regressiva.</p></div>:contagem?<div className="mt-5 bg-gray-900 rounded-xl p-5 border border-blue-900/60"><p className="text-sm text-gray-400 mb-3">Faltam para <b className="text-white">{config.enemNome||"seu ENEM"}</b>:</p><div className="grid grid-cols-4 gap-2 text-center"><div className="bg-gray-800 rounded-lg p-3"><div className="text-2xl md:text-3xl font-bold text-blue-400">{contagem.dias}</div><div className="text-xs text-gray-500 mt-1">dias</div></div><div className="bg-gray-800 rounded-lg p-3"><div className="text-2xl md:text-3xl font-bold text-blue-400">{String(contagem.horas).padStart(2,"0")}</div><div className="text-xs text-gray-500 mt-1">horas</div></div><div className="bg-gray-800 rounded-lg p-3"><div className="text-2xl md:text-3xl font-bold text-blue-400">{String(contagem.minutos).padStart(2,"0")}</div><div className="text-xs text-gray-500 mt-1">minutos</div></div><div className="bg-gray-800 rounded-lg p-3"><div className="text-2xl md:text-3xl font-bold text-blue-400">{String(contagem.segundos).padStart(2,"0")}</div><div className="text-xs text-gray-500 mt-1">segundos</div></div></div><p className="text-xs text-gray-500 mt-3">A contagem considera até 23h59 do dia escolhido.</p></div>:<div className="mt-5 bg-gray-900 rounded-xl p-5 border border-gray-700 text-gray-400">📅 Informe a data da prova para ativar sua contagem regressiva.</div>}
</div>
<div className="bg-gray-800 p-6 rounded-xl border border-gray-700 space-y-5">
<h3 className="text-xl font-bold">📚 Referência de estudo</h3>
<label className="block">Tempo de estudo por dia<select value={config.minutos} onChange={e=>setConfig({...config,minutos:Number(e.target.value)})} className="w-full bg-gray-700 p-3 rounded mt-2">{[60,90,120,150,180].map(x=><option key={x} value={x}>{x}</option>)}</select></label>
<label className="block">Dias de estudo por semana<select value={config.dias} onChange={e=>setConfig({...config,dias:Number(e.target.value)})} className="w-full bg-gray-700 p-3 rounded mt-2">{[3,4,5,6,7].map(x=><option key={x} value={x}>{x}</option>)}</select></label>
<button onClick={()=>{updateState({config});setToast("Configuração salva.")}} className="bg-blue-600 hover:bg-blue-500 px-6 py-3 rounded-lg font-bold">Salvar configuração</button>
</div>
<div className="mt-6 bg-gray-800 p-5 rounded-xl border border-gray-700"><p className="font-bold">📅 Como montar o cronograma</p><p className="text-sm text-gray-400 mt-2">Vá em “Cronograma”, escolha a data, matéria, tópico e duração de cada estudo. Você tem controle total sobre o que estudar.</p></div></div>}

function AppContent(props){const {aba}=props;switch(aba){case"dashboard":return <Dashboard {...props}/>;case"cronograma":return <Cronograma {...props}/>;case"mapa":return <Mapa {...props}/>;case"flashcards":return <Flashcards {...props}/>;case"redacao":return <Redacao {...props}/>;case"simulados":return <SimuladosExternos {...props}/>;case"config":return <Config {...props}/>;default:return <Dashboard {...props}/>}}

function App(){
const [state,setState]=useState(loadState);const [aba,setAba]=useState("dashboard");const [fase,setFase]=useState(null);const [novoCard,setNovoCard]=useState({materia:"Matemática",pergunta:"",resposta:""});const [cardIndex,setCardIndex]=useState(0);const [virado,setVirado]=useState(false);const [config,setConfig]=useState(state.config||{minutos:90,dias:6});const [toast,setToast]=useState("");
useEffect(()=>saveState(state),[state]);useEffect(()=>{if(toast){const t=setTimeout(()=>setToast(""),2200);return()=>clearTimeout(t)}},[toast]);
const totalConcluidos=Object.values(state.topicoStatus||{}).filter(v=>v==="concluido").length;const progressoGeral=Math.round(totalConcluidos/totalTopics()*100);const pendentesRevisao=(state.revisoes||[]).filter(r=>r.flashcardId&&r.proxima<=hoje()).length;
function updateState(patch){setState(s=>({...s,...patch}))}
function setTopicStatus(m,t,status){setState(s=>({...s,topicoStatus:{...s.topicoStatus,[topicKey(m,t)]:status}}))}
function adicionarTarefaManual(data,materia,topico,duracao){if(!data||!materia||!topico){setToast("Preencha data, matéria e tópico.");return false}setState(st=>({...st,cronograma:[...st.cronograma,{id:uid(),data,materia,topico,duracao:Number(duracao)||30,concluido:false,notas:""}].sort((a,b)=>a.data.localeCompare(b.data))}));setToast("Tarefa adicionada ao cronograma.");return true}
function removerTarefa(id){setState(st=>({...st,cronograma:st.cronograma.filter(x=>x.id!==id)}));setToast("Tarefa removida.")}
function toggleTask(id){setState(s=>({...s,cronograma:s.cronograma.map(x=>x.id===id?{...x,concluido:!x.concluido}:x)}));}
function salvarNotasTarefa(id,notas){setState(s=>({...s,cronograma:s.cronograma.map(x=>x.id===id?{...x,notas}:x)}));setToast("Anotações salvas.")}
function excluirRedacao(id){if(!confirm("Tem certeza que deseja excluir esta redação?"))return;setState(s=>({...s,redacoes:s.redacoes.filter(r=>r.id!==id)}));setToast("Redação excluída.")}
function salvarRedacao(dados,editandoId=null){if(!dados?.titulo?.trim()||!dados?.texto?.trim()){setToast("Informe o tema e o texto.");return false}const vals=[dados.c1,dados.c2,dados.c3,dados.c4,dados.c5];const notas=[];for(const raw of vals){if(raw===""||raw===null||raw===undefined){notas.push(0);continue}const n=Number(raw);if(!Number.isInteger(n)||n<0||n>200||n%40!==0){setToast("As competências devem usar 0, 40, 80, 120, 160 ou 200.");return false}notas.push(n)}const total=notas.reduce((a,b)=>a+b,0);setState(s=>{const item={titulo:dados.titulo.trim(),texto:dados.texto,notas,total,data:editandoId?(s.redacoes.find(r=>r.id===editandoId)?.data||hoje()):hoje(),id:editandoId||uid()};return {...s,redacoes:editandoId?s.redacoes.map(r=>r.id===editandoId?{...r,...item}:r):[item,...s.redacoes]}});setToast(editandoId?"Redação atualizada.":"Redação salva.");return true}
function adicionarCard(){if(!novoCard.pergunta.trim()||!novoCard.resposta.trim()){setToast("Preencha pergunta e resposta.");return}setState(s=>({...s,flashcards:[...s.flashcards,{id:uid(),materia:novoCard.materia,pergunta:novoCard.pergunta.trim(),resposta:novoCard.resposta.trim()}]}));setNovoCard({...novoCard,pergunta:"",resposta:""});setToast("Flashcard adicionado.")}
function avaliarCard(status){const card=state.flashcards[cardIndex];if(!card||state.flashcards.length===0)return;const atual=state.revisoes.find(r=>r.flashcardId===card.id);let nivel=atual?.nivel||0;nivel=status==="errou"?0:Math.min(nivel+1,5);const intervalos=[1,3,7,15,30,60];const item={id:atual?.id||uid(),flashcardId:card.id,materia:card.materia,nivel,proxima:addDays(hoje(),intervalos[nivel])};setState(s=>({...s,revisoes:[...s.revisoes.filter(r=>r.flashcardId!==card.id),item]}));setVirado(false);setCardIndex(v=>(v+1)%state.flashcards.length)}
function nav(id){setAba(id)}
const props={state,aba,nav,fase,setFase,setTopicStatus,progressoGeral,totalConcluidos,pendentesRevisao,adicionarTarefaManual,removerTarefa,toggleTask,salvarNotasTarefa,novoCard,setNovoCard,adicionarCard,cardIndex,setCardIndex,virado,setVirado,avaliarCard,salvarRedacao,excluirRedacao,config,setConfig,updateState,setToast};
return <div className="flex h-screen overflow-hidden bg-gray-900"><Sidebar aba={aba} nav={nav}/><main className="flex-1 p-6 md:p-10 overflow-y-auto"><AppContent {...props}/></main>{toast&&<div className="fixed bottom-5 right-5 bg-gray-800 border border-blue-500 px-5 py-3 rounded-xl shadow-xl">{toast}</div>}</div>
}
ReactDOM.createRoot(document.getElementById("root")).render(<App/>);
</script>
</body>
</html>

