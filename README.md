import React, { useState } from 'react';
import { Send, Plus, Trash2, Users, Clock, MessageCircle } from 'lucide-react';

const WhatsAppColetivoApp = () => {
  const [pacientes, setPacientes] = useState([
    { id: 1, nome: '', telefone: '' }
  ]);
  const [mensagemPersonalizada, setMensagemPersonalizada] = useState('');
  const [local, setLocal] = useState('Ambulatório de Referência em Especialidades, na Rua Maivldo Fernandes, sem n°');
  const [horarioInicio, setHorarioInicio] = useState('9h');
  const [horarioFim, setHorarioFim] = useState('15:30h');
  const [dataLimite, setDataLimite] = useState('');
  const [enviando, setEnviando] = useState(false);
  const [mensagensEnviadas, setMensagensEnviadas] = useState([]);

  const adicionarPaciente = () => {
    setPacientes([...pacientes, { id: Date.now(), nome: '', telefone: '' }]);
  };

  const removerPaciente = (id) => {
    setPacientes(pacientes.filter(p => p.id !== id));
  };

  const atualizarPaciente = (id, campo, valor) => {
    setPacientes(pacientes.map(p => 
      p.id === id ? { ...p, [campo]: valor } : p
    ));
  };

  const obterSaudacao = () => {
    const agora = new Date();
    const hora = agora.getHours();
    return hora < 12 ? 'Bom dia' : 'Boa tarde';
  };

  const gerarMensagem = (nomePaciente) => {
    const saudacao = obterSaudacao();
    let mensagemBase = `${saudacao}, ${nomePaciente}, favor retirar sua autorização do procedimento a partir das ${horarioInicio} até as ${horarioFim}. Local: ${local}.`;
    
    if (dataLimite) {
      // Corrige o problema de fuso horário
      const [ano, mes, dia] = dataLimite.split('-');
      const dataFormatada = `${dia}/${mes}/${ano}`;
      mensagemBase += ` PRAZO LIMITE PARA RETIRADA: ${dataFormatada}.`;
    }
    
    return mensagemPersonalizada ? 
      `${mensagemBase}\n\n${mensagemPersonalizada}` : 
      mensagemBase;
  };

  const simularEnvioWhatsApp = async (paciente) => {
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve({
          paciente: paciente.nome,
          telefone: paciente.telefone,
          mensagem: gerarMensagem(paciente.nome),
          horario: new Date().toLocaleTimeString('pt-BR'),
          status: 'enviado'
        });
      }, Math.random() * 1000 + 500);
    });
  };

  const enviarWhatsApp = async () => {
    const pacientesValidos = pacientes.filter(p => p.nome.trim() && p.telefone.trim());
    
    if (pacientesValidos.length === 0) {
      alert('Adicione pelo menos um paciente com nome e telefone válidos.');
      return;
    }

    setEnviando(true);
    setMensagensEnviadas([]);

    try {
      for (const paciente of pacientesValidos) {
        const resultado = await simularEnvioWhatsApp(paciente);
        setMensagensEnviadas(prev => [...prev, resultado]);
      }
      alert(`${pacientesValidos.length} mensagens WhatsApp enviadas com sucesso!`);
    } catch (error) {
      alert('Erro ao enviar mensagens. Tente novamente.');
    } finally {
      setEnviando(false);
    }
  };

  const carregarExemplo = () => {
    setPacientes([
      { id: 1, nome: 'Maria Silva', telefone: '(13) 99999-1234' },
      { id: 2, nome: 'João Santos', telefone: '(13) 98888-5678' },
      { id: 3, nome: 'Ana Costa', telefone: '(13) 97777-9012' }
    ]);
  };

  return (
    <div className="min-h-screen bg-gray-50 p-4">
      <div className="max-w-4xl mx-auto">
        <header className="text-center mb-8">
          <h1 className="text-3xl font-bold text-green-600 mb-2">
            📱 WhatsApp Coletivo - Autorizações Médicas
          </h1>
          <p className="text-gray-600">
            Envie notificações personalizadas via WhatsApp para seus pacientes
          </p>
        </header>

        <div className="grid grid-cols-1 lg:grid-cols-2 gap-8">
          {/* Configurações da Mensagem */}
          <div className="bg-white rounded-lg shadow-md p-6">
            <h2 className="text-xl font-semibold text-gray-800 mb-4 flex items-center">
              <MessageCircle className="mr-2 text-green-600" size={20} />
              Configurar Mensagem
            </h2>
            
            <div className="space-y-4">
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-1">
                  Local da Retirada
                </label>
                <textarea
                  value={local}
                  onChange={(e) => setLocal(e.target.value)}
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500"
                  rows="2"
                />
              </div>

              <div className="grid grid-cols-2 gap-4">
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">
                    Horário Início
                  </label>
                  <input
                    type="text"
                    value={horarioInicio}
                    onChange={(e) => setHorarioInicio(e.target.value)}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500"
                    placeholder="9h"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">
                    Horário Fim
                  </label>
                  <input
                    type="text"
                    value={horarioFim}
                    onChange={(e) => setHorarioFim(e.target.value)}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500"
                    placeholder="15:30h"
                  />
                </div>
              </div>

              <div>
                <label className="block text-sm font-medium text-gray-700 mb-1">
                  Data Limite para Retirada
                </label>
                <input
                  type="date"
                  value={dataLimite}
                  onChange={(e) => setDataLimite(e.target.value)}
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500"
                />
                <p className="text-xs text-gray-500 mt-1">
                  Opcional - será incluído na mensagem se preenchido
                </p>
              </div>

              <div>
                <label className="block text-sm font-medium text-gray-700 mb-1">
                  Mensagem Adicional (Opcional)
                </label>
                <textarea
                  value={mensagemPersonalizada}
                  onChange={(e) => setMensagemPersonalizada(e.target.value)}
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-green-500"
                  rows="3"
                  placeholder="Digite uma mensagem adicional se necessário..."
                />
              </div>

              <div className="bg-green-50 border border-green-200 rounded-md p-3">
                <h4 className="font-medium text-green-800 mb-2 flex items-center">
                  <Clock className="mr-2" size={16} />
                  Prévia da Mensagem
                </h4>
                <p className="text-sm text-green-700">
                  {gerarMensagem('[Nome do Paciente]')}
                </p>
              </div>
            </div>
          </div>

          {/* Lista de Pacientes */}
          <div className="bg-white rounded-lg shadow-md p-6">
            <div className="flex items-center justify-between mb-4">
              <h2 className="text-xl font-semibold text-gray-800 flex items-center">
                <Users className="mr-2 text-green-600" size={20} />
                Lista de Pacientes ({pacientes.length})
              </h2>
              <button
                onClick={carregarExemplo}
                className="text-sm bg-gray-100 hover:bg-gray-200 px-3 py-1 rounded-md transition-colors"
              >
                Carregar Exemplo
              </button>
            </div>

            <div className="space-y-3 max-h-64 overflow-y-auto">
              {/* Cabeçalho */}
              <div className="flex gap-2 items-center text-sm font-medium text-gray-600 border-b pb-2">
                <div className="flex-1">Nome do Paciente</div>
                <div className="flex-1">Telefone</div>
                <div className="w-10"></div>
              </div>
              
              {pacientes.map((paciente) => (
                <div key={paciente.id} className="flex gap-2 items-center">
                  <input
                    type="text"
                    placeholder="Nome do paciente"
                    value={paciente.nome}
                    onChange={(e) => atualizarPaciente(paciente.id, 'nome', e.target.value)}
                    className="flex-1 px-3 py-2 border border-gray-300 rounded-md text-sm focus:outline-none focus:ring-2 focus:ring-green-500"
                  />
                  <input
                    type="tel"
                    placeholder="(13) 99999-9999"
                    value={paciente.telefone}
                    onChange={(e) => atualizarPaciente(paciente.id, 'telefone', e.target.value)}
                    className="flex-1 px-3 py-2 border border-gray-300 rounded-md text-sm focus:outline-none focus:ring-2 focus:ring-green-500"
                  />
                  <button
                    onClick={() => removerPaciente(paciente.id)}
                    className="p-2 text-red-600 hover:bg-red-50 rounded-md transition-colors"
                  >
                    <Trash2 size={16} />
                  </button>
                </div>
              ))}
            </div>

            <div className="flex gap-2 mt-4">
              <button
                onClick={adicionarPaciente}
                className="flex items-center gap-2 px-4 py-2 bg-green-600 text-white rounded-md hover:bg-green-700 transition-colors text-sm"
              >
                <Plus size={16} />
                Adicionar Paciente
              </button>

              <button
                onClick={enviarWhatsApp}
                disabled={enviando}
                className="flex items-center gap-2 px-4 py-2 bg-green-600 text-white rounded-md hover:bg-green-700 disabled:bg-gray-400 transition-colors text-sm"
              >
                <Send size={16} />
                {enviando ? 'Enviando...' : 'Enviar WhatsApp'}
              </button>
            </div>
          </div>
        </div>

        {/* Log de Mensagens */}
        {mensagensEnviadas.length > 0 && (
          <div className="mt-8 bg-white rounded-lg shadow-md p-6">
            <h3 className="text-lg font-semibold text-gray-800 mb-4">
              📋 Mensagens Enviadas
            </h3>
            <div className="space-y-2 max-h-64 overflow-y-auto">
              {mensagensEnviadas.map((msg, index) => (
                <div key={index} className="bg-green-50 border border-green-200 rounded-md p-3">
                  <div className="flex justify-between items-start">
                    <div>
                      <p className="font-medium text-green-800">
                        ✅ {msg.paciente}
                      </p>
                      <p className="text-sm text-green-600">
                        📞 {msg.telefone}
                      </p>
                      <p className="text-sm text-green-600 mt-1">
                        💬 {msg.mensagem}
                      </p>
                    </div>
                    <span className="text-xs text-green-600">
                      {msg.horario}
                    </span>
                  </div>
                </div>
              ))}
            </div>
          </div>
        )}
      </div>
    </div>
  );
};

export default WhatsAppColetivoApp;
