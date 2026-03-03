# darkhold-app
import React, { useState, useEffect } from 'react';
import { 
  Shield, 
  Wifi, 
  Activity, 
  Lock, 
  Unlock, 
  Users, 
  Terminal,
  Zap,
  Globe,
  Settings
} from 'lucide-react';

const App = () => {
  const [activeTab, setActiveTab] = useState('dashboard');
  const [isScanning, setIsScanning] = useState(false);
  const [logs, setLogs] = useState([
    "Sessão Darkhold iniciada...",
    "A aguardar comandos de rede..."
  ]);
  const [isHotspotActive, setIsHotspotActive] = useState(false);

  const addLog = (msg) => {
    setLogs(prev => [`[${new Date().toLocaleTimeString()}] ${msg}`, ...prev].slice(0, 10));
  };

  const handleScan = () => {
    setIsScanning(true);
    addLog("A iniciar scan de redes circundantes...");
    setTimeout(() => {
      setIsScanning(false);
      addLog("Scan concluído. 4 redes detetadas.");
    }, 3000);
  };

  const toggleHotspot = () => {
    setIsHotspotActive(!isHotspotActive);
    addLog(isHotspotActive ? "Hotspot desativado." : "Modo Roteador Darkhold ativo. Tráfego a ser monitorizado.");
  };

  return (
    <div className="min-h-screen bg-slate-950 text-emerald-500 font-mono p-4 md:p-8">
      {/* Header */}
      <header className="max-w-6xl mx-auto mb-8 flex justify-between items-center border-b border-emerald-900/50 pb-4">
        <div className="flex items-center gap-3">
          <div className="bg-emerald-950 p-2 rounded-lg border border-emerald-500 shadow-[0_0_15px_rgba(16,185,129,0.3)]">
            <Shield size={32} />
          </div>
          <div>
            <h1 className="text-2xl font-bold tracking-tighter uppercase">Darkhold v2.0</h1>
            <p className="text-xs text-emerald-700">Auditoria & Gestão de Tráfego</p>
          </div>
        </div>
        <div className="flex gap-4">
          <div className={`px-3 py-1 rounded-full text-xs flex items-center gap-2 border ${isHotspotActive ? 'border-emerald-500 bg-emerald-500/10' : 'border-red-900 bg-red-950/20 text-red-700'}`}>
            <span className={`w-2 h-2 rounded-full ${isHotspotActive ? 'bg-emerald-500 animate-pulse' : 'bg-red-700'}`}></span>
            {isHotspotActive ? 'HOTSPOT ATIVO' : 'HOTSPOT OFF'}
          </div>
        </div>
      </header>

      <main className="max-w-6xl mx-auto grid grid-cols-1 lg:grid-cols-3 gap-6">
        
        {/* Navigation Sidebar */}
        <nav className="flex flex-col gap-2">
          <NavButton active={activeTab === 'dashboard'} onClick={() => setActiveTab('dashboard')} icon={<Activity size={18}/>} label="Dashboard Principal" />
          <NavButton active={activeTab === 'scanner'} onClick={() => setActiveTab('scanner')} icon={<Wifi size={18}/>} label="Scanner de Redes" />
          <NavButton active={activeTab === 'traffic'} onClick={() => setActiveTab('traffic')} icon={<Globe size={18}/>} label="Análise de Tráfego" />
          <NavButton active={activeTab === 'settings'} onClick={() => setActiveTab('settings')} icon={<Settings size={18}/>} label="Configurações" />
          
          <div className="mt-6 p-4 border border-emerald-900 bg-black/40 rounded-xl">
            <h3 className="text-sm font-bold mb-4 flex items-center gap-2">
              <Terminal size={14}/> LIVE LOGS
            </h3>
            <div className="text-[10px] space-y-2 h-32 overflow-y-auto scrollbar-hide">
              {logs.map((log, i) => (
                <div key={i} className="opacity-80 border-l border-emerald-800 pl-2">{log}</div>
              ))}
            </div>
          </div>
        </nav>

        {/* Main Content Area */}
        <section className="lg:col-span-2 space-y-6">
          {activeTab === 'dashboard' && (
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <StatusCard 
                title="Modo Roteador" 
                status={isHotspotActive ? "A transmitir" : "Inativo"} 
                description="Encaminha todos os pacotes através deste terminal."
                action={toggleHotspot}
                actionLabel={isHotspotActive ? "Parar" : "Ativar"}
                icon={<Zap className={isHotspotActive ? "text-yellow-400" : "text-emerald-900"}/>}
              />
              <StatusCard 
                title="Auditoria de Segurança" 
                status="Pendente" 
                description="Verifica vulnerabilidades no protocolo de rede atual."
                action={handleScan}
                actionLabel="Escanear"
                isLoading={isScanning}
                icon={<Lock/>}
              />
              
              <div className="md:col-span-2 bg-emerald-950/20 border border-emerald-900 p-6 rounded-2xl relative overflow-hidden">
                <div className="absolute top-0 right-0 p-4 opacity-10">
                  <Activity size={80} />
                </div>
                <h3 className="text-lg font-bold mb-4 flex items-center gap-2">
                  <Users size={20}/> Dispositivos Ligados
                </h3>
                {isHotspotActive ? (
                  <div className="space-y-4">
                    <ConnectedDevice ip="192.168.1.45" mac="00:1A:2B:3C:4D:5E" traffic="1.2 GB" />
                    <ConnectedDevice ip="192.168.1.12" mac="A1:B2:C3:D4:E5:F6" traffic="450 MB" />
                  </div>
                ) : (
                  <p className="text-emerald-800 italic">Ativa o modo roteador para ver dispositivos ligados.</p>
                )}
              </div>
            </div>
          )}

          {activeTab === 'scanner' && (
            <div className="bg-black/40 border border-emerald-900 p-6 rounded-2xl">
              <div className="flex justify-between items-center mb-6">
                <h2 className="text-xl font-bold">Redes Próximas</h2>
                <button 
                  onClick={handleScan}
                  disabled={isScanning}
                  className="bg-emerald-500 text-black px-4 py-2 rounded-lg font-bold hover:bg-emerald-400 disabled:opacity-50"
                >
                  {isScanning ? "A processar..." : "Novo Scan"}
                </button>
              </div>
              <div className="space-y-3">
                <WifiNetwork name="Home_Fiber_5G" signal="90%" security="WPA3" />
                <WifiNetwork name="Coffee_Free_WiFi" signal="45%" security="Aberto" dangerous />
                <WifiNetwork name="Corporate_HQ" signal="60%" security="WPA2-Enterprise" />
              </div>
            </div>
          )}

          {activeTab === 'traffic' && (
            <div className="bg-black/40 border border-emerald-900 p-6 rounded-2xl">
              <h2 className="text-xl font-bold mb-4">Analisador de Pacotes</h2>
              <div className="bg-black p-4 rounded-lg border border-emerald-900 font-mono text-xs h-64 overflow-y-auto">
                <p className="text-emerald-400">[TCP] 192.168.1.45:443 {"->"} 104.26.10.22:443 (ACK)</p>
                <p className="text-emerald-600">[DNS] Query: google.com (Type A)</p>
                <p className="text-emerald-400">[UDP] 192.168.1.12:53211 {"->"} 8.8.8.8:53</p>
                <p className="text-red-500">[WARN] Possível tentativa de brute-force detetada no IP 192.168.1.100</p>
                <div className="animate-pulse">_</div>
              </div>
            </div>
          )}
        </section>
      </main>
    </div>
  );
};

const NavButton = ({ active, onClick, icon, label }) => (
  <button 
    onClick={onClick}
    className={`flex items-center gap-3 px-4 py-3 rounded-xl transition-all duration-200 ${
      active ? 'bg-emerald-500 text-black font-bold shadow-lg shadow-emerald-500/20' : 'hover:bg-emerald-900/30 text-emerald-700'
    }`}
  >
    {icon} {label}
  </button>
);

const StatusCard = ({ title, status, description, action, actionLabel, icon, isLoading }) => (
  <div className="bg-black/40 border border-emerald-900 p-6 rounded-2xl flex flex-col justify-between">
    <div>
      <div className="flex justify-between items-start mb-2">
        <h3 className="font-bold text-emerald-200">{title}</h3>
        <span className="p-2 bg-emerald-950 rounded-lg">{icon}</span>
      </div>
      <p className="text-2xl font-black mb-1">{status}</p>
      <p className="text-xs text-emerald-800 mb-6">{description}</p>
    </div>
    <button 
      onClick={action}
      className="w-full py-2 border border-emerald-500/30 rounded-lg hover:bg-emerald-500/10 transition-colors"
    >
      {isLoading ? "A carregar..." : actionLabel}
    </button>
  </div>
);

const ConnectedDevice = ({ ip, mac, traffic }) => (
  <div className="flex justify-between items-center p-3 bg-emerald-950/30 rounded-xl border border-emerald-900/50">
    <div className="flex items-center gap-3">
      <div className="p-2 bg-emerald-900/50 rounded-full">
        <Users size={16}/>
      </div>
      <div>
        <p className="text-sm font-bold text-emerald-300">{ip}</p>
        <p className="text-[10px] opacity-60">MAC: {mac}</p>
      </div>
    </div>
    <div className="text-right">
      <p className="text-xs font-bold">{traffic}</p>
      <p className="text-[10px] text-emerald-700 uppercase">Dados</p>
    </div>
  </div>
);

const WifiNetwork = ({ name, signal, security, dangerous }) => (
  <div className={`flex justify-between items-center p-4 rounded-xl border ${dangerous ? 'border-red-900/50 bg-red-950/10' : 'border-emerald-900/30 bg-black/20'}`}>
    <div className="flex items-center gap-4">
      <Wifi size={20} className={dangerous ? 'text-red-500' : 'text-emerald-500'}/>
      <div>
        <p className="font-bold text-sm">{name}</p>
        <p className="text-[10px] opacity-70">{security} • Sinal: {signal}</p>
      </div>
    </div>
    <button className={`px-3 py-1 rounded-md text-[10px] font-bold border ${dangerous ? 'border-red-500 text-red-500' : 'border-emerald-500'}`}>
      ANALISAR
    </button>
  </div>
);

export default App;
