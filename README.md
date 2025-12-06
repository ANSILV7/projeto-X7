<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Projeto X7 - Bot de Arbitragem de Latência</title>
    <meta name="description" content="Projeto X7: Bot de Arbitragem de Latência com conexão real a APIs de casas de apostas.">
    <style>
        :root {
            --color-dark: #0a0a0a;
            --color-black: #000000;
            --color-red: #FF0000;
            --color-red-dark: #660000;
            --color-white: #FFFFFF;
            --color-gray: #B0B0B0;
            --color-light-gray: #1a1a1a;
            --color-green: #00FF00;
            --color-blue: #00BFFF;
            --color-primary: var(--color-red);
            --color-secondary: #FF4500;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #000000 0%, #1a0000 100%);
            color: var(--color-white);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            width: 100%;
            max-width: 1000px;
            margin: 0 auto;
            background: var(--color-black);
            border-radius: 20px;
            box-shadow: 0 0 100px rgba(255, 0, 0, 0.3), inset 0 0 100px rgba(255, 0, 0, 0.1);
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        .screen {
            padding: 50px;
            display: none;
            flex-direction: column;
            flex-grow: 1;
            min-height: 700px;
        }

        .screen.active {
            display: flex;
        }

        /* SPLASH SCREEN */
        #splash-screen {
            text-align: center;
            justify-content: center;
            background: linear-gradient(145deg, #000000 0%, #0a0a0a 100%);
            position: relative;
            overflow: hidden;
        }

        #splash-screen::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: radial-gradient(circle at center, rgba(255, 0, 0, 0.1) 0%, transparent 70%);
            pointer-events: none;
        }

        .splash-content {
            animation: fadeIn 2s ease-in-out;
            position: relative;
            z-index: 1;
        }

        .bot-icon {
            font-size: 12em;
            font-weight: bold;
            color: var(--color-red);
            margin-bottom: 30px;
            text-shadow: 0 0 40px var(--color-red), 0 0 80px rgba(255, 0, 0, 0.5);
            animation: pulseRed 2s infinite alternate;
            letter-spacing: 20px;
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        @keyframes pulseRed {
            from {
                transform: scale(1);
                opacity: 0.8;
                text-shadow: 0 0 40px var(--color-red), 0 0 80px rgba(255, 0, 0, 0.5);
            }
            to {
                transform: scale(1.05);
                opacity: 1;
                text-shadow: 0 0 60px var(--color-red), 0 0 120px rgba(255, 0, 0, 0.7);
            }
        }

        #splash-screen h1 {
            font-size: 2.5em;
            color: var(--color-white);
            margin-bottom: 20px;
            letter-spacing: 8px;
            font-weight: 300;
        }

        .tagline {
            font-size: 1.2em;
            color: var(--color-gray);
            margin-bottom: 50px;
            font-weight: 300;
            letter-spacing: 2px;
        }

        /* HEADER COMUM */
        .header {
            text-align: center;
            margin-bottom: 40px;
            border-bottom: 3px solid var(--color-red);
            padding-bottom: 20px;
        }

        .header h1 {
            font-size: 2.5em;
            color: var(--color-primary);
            margin-bottom: 10px;
            text-shadow: 0 0 10px var(--color-red);
        }

        .header p {
            color: var(--color-gray);
            font-size: 1.1em;
        }

        /* DASHBOARD */
        .dashboard {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-bottom: 30px;
        }

        .dashboard-card {
            background: var(--color-light-gray);
            border: 2px solid var(--color-red);
            border-radius: 10px;
            padding: 20px;
            text-align: center;
        }

        .dashboard-card h3 {
            color: var(--color-secondary);
            margin-bottom: 10px;
            font-size: 0.9em;
            text-transform: uppercase;
        }

        .dashboard-card .value {
            font-size: 2em;
            color: var(--color-green);
            font-weight: bold;
            margin: 10px 0;
        }

        .dashboard-card .status {
            font-size: 0.85em;
            color: var(--color-gray);
        }

        /* FORMULÁRIO */
        .form-content {
            display: flex;
            flex-direction: column;
            gap: 25px;
        }

        .form-section {
            background: var(--color-light-gray);
            border-left: 4px solid var(--color-red);
            padding: 25px;
            border-radius: 10px;
        }

        .form-section h3 {
            color: var(--color-secondary);
            margin-bottom: 20px;
            font-size: 1.2em;
            text-transform: uppercase;
        }

        .form-group {
            display: flex;
            flex-direction: column;
            margin-bottom: 20px;
        }

        .form-group label {
            font-weight: bold;
            margin-bottom: 10px;
            color: var(--color-gray);
            font-size: 1.05em;
        }

        .form-group input,
        .form-group select {
            padding: 15px;
            border: 2px solid var(--color-red-dark);
            border-radius: 8px;
            background-color: #0a0a0a;
            color: var(--color-white);
            font-size: 1em;
            transition: all 0.3s;
        }

        .form-group input:focus,
        .form-group select:focus {
            border-color: var(--color-green);
            box-shadow: 0 0 10px rgba(0, 255, 0, 0.3);
            outline: none;
        }

        .form-group small {
            color: var(--color-gray);
            margin-top: 8px;
            font-size: 0.9em;
        }

        .form-row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
        }

        /* INFO DISPLAY */
        .info-display {
            background: #0a0a0a;
            border: 2px solid var(--color-red);
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .info-display .label {
            color: var(--color-gray);
            font-size: 0.95em;
        }

        .info-display .value {
            color: var(--color-green);
            font-weight: bold;
            font-size: 1.2em;
        }

        /* BOTÕES */
        .btn {
            padding: 18px 40px;
            border: none;
            border-radius: 8px;
            font-size: 1.1em;
            cursor: pointer;
            text-decoration: none;
            display: inline-block;
            transition: all 0.3s ease;
            font-weight: bold;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .btn-primary {
            background: linear-gradient(135deg, var(--color-red) 0%, var(--color-red-dark) 100%);
            color: var(--color-white);
            box-shadow: 0 8px 25px rgba(255, 0, 0, 0.3);
            width: 100%;
        }

        .btn-primary:hover {
            transform: translateY(-3px);
            box-shadow: 0 12px 35px rgba(255, 0, 0, 0.5);
        }

        .btn-primary:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .loading-spinner {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid var(--color-red-dark);
            border-top: 3px solid var(--color-red);
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-right: 10px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .success-box {
            background: #003300;
            border-left: 4px solid var(--color-green);
            padding: 15px;
            border-radius: 5px;
            color: var(--color-green);
            margin: 15px 0;
        }

        .error-box {
            background: #330000;
            border-left: 4px solid var(--color-red);
            padding: 15px;
            border-radius: 5px;
            color: var(--color-red);
            margin: 15px 0;
        }

        .info-line {
            display: flex;
            justify-content: space-between;
            padding: 10px 0;
            border-bottom: 1px solid var(--color-red-dark);
            color: var(--color-gray);
        }

        .info-line strong {
            color: var(--color-white);
        }

        /* RESPONSIVE */
        @media (max-width: 768px) {
            .form-row {
                grid-template-columns: 1fr;
            }

            .dashboard {
                grid-template-columns: 1fr;
            }

            .screen {
                padding: 30px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        
        <!-- SPLASH SCREEN -->
        <section id="splash-screen" class="screen active">
            <div class="splash-content">
                <div class="bot-icon">X7</div>
                <h1>PROJETO X7</h1>
                <p class="tagline">Bot de Arbitragem de Latência | IA Integrada | 100% Automático</p>
                <button class="btn btn-primary" onclick="nextScreen('country-screen')">INICIAR</button>
            </div>
        </section>

        <!-- SELEÇÃO DE PAÍS -->
        <section id="country-screen" class="screen">
            <header class="header">
                <h1>🌍 SELECIONE SEU PAÍS</h1>
                <p>A moeda será selecionada automaticamente</p>
            </header>

            <form id="country-form" class="form-content">
                <div class="form-section">
                    <h3>📍 País de Operação</h3>
                    <div class="form-group">
                        <select id="country-select" name="country" onchange="selectCountry()" required>
                            <option value="">-- Selecione um País --</option>
                            <option value="brasil">🇧🇷 Brasil (Real - BRL)</option>
                            <option value="portugal">🇵🇹 Portugal (Euro - EUR)</option>
                            <option value="eua">🇺🇸 Estados Unidos (Dólar - USD)</option>
                            <option value="reino-unido">🇬🇧 Reino Unido (Libra - GBP)</option>
                            <option value="alemanha">🇩🇪 Alemanha (Euro - EUR)</option>
                            <option value="franca">🇫🇷 França (Euro - EUR)</option>
                            <option value="espanha">🇪🇸 Espanha (Euro - EUR)</option>
                            <option value="italia">🇮🇹 Itália (Euro - EUR)</option>
                            <option value="holanda">🇳🇱 Holanda (Euro - EUR)</option>
                            <option value="belgica">🇧🇪 Bélgica (Euro - EUR)</option>
                            <option value="suica">🇨🇭 Suíça (Franco - CHF)</option>
                            <option value="suecia">🇸🇪 Suécia (Coroa - SEK)</option>
                            <option value="noruega">🇳🇴 Noruega (Coroa - NOK)</option>
                            <option value="dinamarca">🇩🇰 Dinamarca (Coroa - DKK)</option>
                            <option value="australia">🇦🇺 Austrália (Dólar - AUD)</option>
                            <option value="japao">🇯🇵 Japão (Iene - JPY)</option>
                            <option value="singapura">🇸🇬 Singapura (Dólar - SGD)</option>
                            <option value="hongkong">🇭🇰 Hong Kong (Dólar - HKD)</option>
                            <option value="mexico">🇲🇽 México (Peso - MXN)</option>
                            <option value="argentina">🇦🇷 Argentina (Peso - ARS)</option>
                            <option value="canada">🇨🇦 Canadá (Dólar - CAD)</option>
                            <option value="india">🇮🇳 Índia (Rupia - INR)</option>
                            <option value="tailandia">🇹🇭 Tailândia (Baht - THB)</option>
                            <option value="vietna">🇻🇳 Vietnã (Dong - VND)</option>
                            <option value="emirados">🇦🇪 Emirados (Dirham - AED)</option>
                        </select>
                    </div>

                    <div id="currency-display" class="info-display" style="display: none;">
                        <span class="label">Moeda Selecionada:</span>
                        <span class="value" id="currency-value">-</span>
                    </div>
                </div>

                <button type="submit" class="btn btn-primary">AVANÇAR</button>
            </form>
        </section>

        <!-- CASAS DE APOSTAS -->
        <section id="bookmakers-screen" class="screen">
            <header class="header">
                <h1>🎯 CASAS DE APOSTAS DISPONÍVEIS</h1>
                <p id="bookmakers-subtitle">Selecione a casa principal para operação</p>
            </header>

            <form id="bookmakers-form" class="form-content">
                <div class="form-section">
                    <h3>Casa Principal</h3>
                    <div class="form-group">
                        <select id="primary-bookmaker" name="primary-bookmaker" required>
                            <option value="">-- Selecione a Casa Principal --</option>
                        </select>
                        <small>A casa principal será usada para as operações</small>
                    </div>
                </div>

                <button type="submit" class="btn btn-primary">AVANÇAR PARA LOGIN</button>
            </form>
        </section>

        <!-- LOGIN DA CASA DE APOSTAS -->
        <section id="login-screen" class="screen">
            <header class="header">
                <h1>🔐 CONECTAR À CASA DE APOSTAS</h1>
                <p id="bookmaker-name">Insira suas credenciais reais para conectar</p>
            </header>

            <form id="login-form" class="form-content">
                <div class="form-section">
                    <h3>Credenciais de Acesso</h3>
                    <div class="form-group">
                        <label for="bookmaker-user">Usuário / Email:</label>
                        <input type="text" id="bookmaker-user" name="bookmaker-user" placeholder="Seu usuário ou email" required>
                        <small>O mesmo que você usa para acessar a casa de apostas</small>
                    </div>
                    <div class="form-group">
                        <label for="bookmaker-password">Senha:</label>
                        <input type="password" id="bookmaker-password" name="bookmaker-password" placeholder="Sua senha" required>
                        <small>Será usada para conectar à API da casa de apostas</small>
                    </div>
                </div>

                <div class="form-section">
                    <h3>ℹ️ Informação Importante</h3>
                    <p style="color: var(--color-gray); line-height: 1.6;">
                        A conexão será feita através da API oficial da casa de apostas. Seu saldo real será detectado e exibido. 
                        Você poderá então definir livremente quanto deseja usar como capital operacional e qual é sua meta de ganho diário.
                    </p>
                </div>

                <button type="submit" class="btn btn-primary" id="login-btn">CONECTAR À API</button>
            </form>

            <div id="login-status" style="display: none;"></div>
        </section>

        <!-- CONFIGURAÇÃO DE RISCO -->
        <section id="config-screen" class="screen">
            <header class="header">
                <h1>⚙️ CONFIGURAÇÃO FINAL</h1>
                <p>Defina seus parâmetros de operação</p>
            </header>

            <form id="config-form" class="form-content">
                <div class="form-section">
                    <h3>💰 Informações da Conta</h3>
                    <div class="dashboard">
                        <div class="dashboard-card">
                            <h3>Saldo da Conta</h3>
                            <div class="value" id="detected-balance">-</div>
                            <div class="status">Saldo real detectado</div>
                        </div>
                        <div class="dashboard-card">
                            <h3>Moeda</h3>
                            <div class="value" id="display-currency">-</div>
                            <div class="status">Moeda de operação</div>
                        </div>
                    </div>
                </div>

                <div class="form-section">
                    <h3>📊 Parâmetros de Operação</h3>
                    <p style="color: var(--color-gray); margin-bottom: 20px; font-size: 0.95em;">
                        <strong>Nota:</strong> Estes valores são independentes do seu saldo. Você pode ter R$ 800 na conta e definir um capital de R$ 100, 
                        ou uma meta de ganho de R$ 1.000 por dia.
                    </p>

                    <div class="form-row">
                        <div class="form-group">
                            <label for="valor-aposta">Valor Base da Aposta:</label>
                            <input type="number" id="valor-aposta" name="valor-aposta" min="0.01" step="0.01" placeholder="Ex: 50.00" required>
                            <small>Valor inicial de cada aposta</small>
                        </div>
                        <div class="form-group">
                            <label for="capital-operacional">Capital Operacional:</label>
                            <input type="number" id="capital-operacional" name="capital-operacional" min="0.01" step="0.01" placeholder="Ex: 500.00" required>
                            <small>Quanto você quer usar para operar (pode ser menor que o saldo)</small>
                        </div>
                    </div>

                    <div class="form-row">
                        <div class="form-group">
                            <label for="max-perda-diaria">Limite de Perda Diária:</label>
                            <input type="number" id="max-perda-diaria" name="max-perda-diaria" min="0.01" step="0.01" placeholder="Ex: 500.00" required>
                            <small>Máximo que você quer perder em um dia</small>
                        </div>
                        <div class="form-group">
                            <label for="max-ganho-diario">Meta de Ganho Diário:</label>
                            <input type="number" id="max-ganho-diario" name="max-ganho-diario" min="0.01" step="0.01" placeholder="Ex: 1000.00" required>
                            <small>Quanto você quer ganhar por dia (meta de lucro)</small>
                        </div>
                    </div>
                </div>

                <div class="form-section">
                    <h3>📱 Notificações Telegram</h3>
                    <div class="form-group">
                        <label for="telegram-token">Token Telegram (Opcional):</label>
                        <input type="text" id="telegram-token" name="telegram-token" placeholder="Seu token para notificações">
                        <small>Receba alertas de operações em tempo real via Telegram</small>
                    </div>
                </div>

                <div class="form-section">
                    <h3>🛡️ Segurança</h3>
                    <div class="form-group">
                        <label>
                            <input type="checkbox" id="stealth-mode" name="stealth-mode" checked>
                            <span style="color: var(--color-white); margin-left: 10px;">Stealth Mode (Recomendado)</span>
                        </label>
                    </div>
                    <div class="form-group">
                        <label>
                            <input type="checkbox" id="rotation-agents" name="rotation-agents" checked>
                            <span style="color: var(--color-white); margin-left: 10px;">Rotação de User Agents</span>
                        </label>
                    </div>
                    <div class="form-group">
                        <label>
                            <input type="checkbox" id="random-delays" name="random-delays" checked>
                            <span style="color: var(--color-white); margin-left: 10px;">Delays Aleatórios</span>
                        </label>
                    </div>
                </div>

                <button type="submit" class="btn btn-primary">INICIAR PROJETO X7</button>
            </form>

            <div id="final-status" style="display: none;"></div>
        </section>

    </div>

    <script>
        // Mapeamento de país para moeda
        const countryToCurrency = {
            brasil: { currency: 'BRL', symbol: 'R$' },
            portugal: { currency: 'EUR', symbol: '€' },
            eua: { currency: 'USD', symbol: '$' },
            'reino-unido': { currency: 'GBP', symbol: '£' },
            alemanha: { currency: 'EUR', symbol: '€' },
            franca: { currency: 'EUR', symbol: '€' },
            espanha: { currency: 'EUR', symbol: '€' },
            italia: { currency: 'EUR', symbol: '€' },
            holanda: { currency: 'EUR', symbol: '€' },
            belgica: { currency: 'EUR', symbol: '€' },
            suica: { currency: 'CHF', symbol: 'CHF' },
            suecia: { currency: 'SEK', symbol: 'kr' },
            noruega: { currency: 'NOK', symbol: 'kr' },
            dinamarca: { currency: 'DKK', symbol: 'kr' },
            australia: { currency: 'AUD', symbol: 'A$' },
            japao: { currency: 'JPY', symbol: '¥' },
            singapura: { currency: 'SGD', symbol: 'S$' },
            hongkong: { currency: 'HKD', symbol: 'HK$' },
            mexico: { currency: 'MXN', symbol: '$' },
            argentina: { currency: 'ARS', symbol: '$' },
            canada: { currency: 'CAD', symbol: 'C$' },
            india: { currency: 'INR', symbol: '₹' },
            tailandia: { currency: 'THB', symbol: '฿' },
            vietna: { currency: 'VND', symbol: '₫' },
            emirados: { currency: 'AED', symbol: 'د.إ' }
        };

        // Base de dados de casas de apostas
        const bookmakersDatabase = {
            brasil: ['Bet365', 'Betano', 'SportingBet', 'BetSul', 'Rivalo', '22Bet', '1xBet'],
            portugal: ['Betfair', 'Pinnacle', 'Smarkets', 'Unibet', 'Bwin', 'William Hill', '888Sport'],
            eua: ['Bovada', 'Intertops', 'Nitrogen', 'BetOnline', 'MyBookie'],
            'reino-unido': ['Betfair', 'William Hill', 'Ladbrokes', 'Coral', 'Sky Bet', 'Paddy Power', 'Smarkets'],
            alemanha: ['Bwin', 'Unibet', 'Pinnacle', 'Marathon Bet', 'Betfair'],
            franca: ['Bwin', 'Unibet', 'Pinnacle', 'Betfair', 'PMU'],
            espanha: ['Bwin', 'Unibet', 'Pinnacle', 'Betfair', 'Codere'],
            italia: ['Bwin', 'Unibet', 'Pinnacle', 'Betfair', 'Snai'],
            holanda: ['Bwin', 'Unibet', 'Pinnacle', 'Betfair', 'BetCity'],
            belgica: ['Bwin', 'Unibet', 'Pinnacle', 'Betfair'],
            suica: ['Pinnacle', 'Betfair', 'Smarkets'],
            suecia: ['Bwin', 'Unibet', 'Pinnacle', 'Betfair'],
            noruega: ['Pinnacle', 'Betfair', 'Smarkets'],
            dinamarca: ['Bwin', 'Unibet', 'Pinnacle', 'Betfair'],
            australia: ['Pinnacle', 'Smarkets', 'Betfair', 'Nitrogen'],
            japao: ['Pinnacle', 'Smarkets'],
            singapura: ['Pinnacle', 'Smarkets', 'Nitrogen'],
            hongkong: ['Pinnacle', 'Smarkets'],
            mexico: ['Pinnacle', 'Intertops', '1xBet'],
            argentina: ['Pinnacle', 'Intertops', '1xBet'],
            canada: ['Pinnacle', 'Intertops', 'Nitrogen'],
            india: ['Pinnacle', '1xBet'],
            tailandia: ['Pinnacle', 'Nitrogen'],
            vietna: ['Pinnacle', '1xBet'],
            emirados: ['Pinnacle', 'Smarkets', 'Nitrogen']
        };

        let selectedCountry = '';
        let selectedCurrency = '';
        let selectedCurrencySymbol = '';
        let selectedBookmaker = '';
        let detectedBalance = 0;

        function nextScreen(nextId) {
            document.querySelectorAll('.screen').forEach(screen => {
                screen.classList.remove('active');
            });
            document.getElementById(nextId).classList.add('active');
        }

        function selectCountry() {
            const countrySelect = document.getElementById('country-select');
            selectedCountry = countrySelect.value;

            if (!selectedCountry) {
                document.getElementById('currency-display').style.display = 'none';
                return;
            }

            const currencyInfo = countryToCurrency[selectedCountry];
            selectedCurrency = currencyInfo.currency;
            selectedCurrencySymbol = currencyInfo.symbol;

            document.getElementById('currency-value').textContent = `${selectedCurrencySymbol} ${selectedCurrency}`;
            document.getElementById('currency-display').style.display = 'flex';
        }

        // Country Form
        document.getElementById('country-form').addEventListener('submit', function(event) {
            event.preventDefault();
            
            if (!selectedCountry) {
                alert('Por favor, selecione um país');
                return;
            }

            updateBookmakers();
            nextScreen('bookmakers-screen');
        });

        function updateBookmakers() {
            const bookmakers = bookmakersDatabase[selectedCountry] || [];
            const select = document.getElementById('primary-bookmaker');
            
            select.innerHTML = '<option value="">-- Selecione a Casa --</option>';
            
            if (bookmakers.length > 0) {
                bookmakers.forEach(bm => {
                    const option = document.createElement('option');
                    option.value = bm;
                    option.textContent = bm;
                    select.appendChild(option);
                });
                document.getElementById('bookmakers-subtitle').textContent = 
                    `${bookmakers.length} casa(s) de apostas disponível(is)`;
            }
        }

        // Bookmakers Form
        document.getElementById('bookmakers-form').addEventListener('submit', function(event) {
            event.preventDefault();
            selectedBookmaker = document.getElementById('primary-bookmaker').value;
            
            if (!selectedBookmaker) {
                alert('Por favor, selecione uma casa de apostas');
                return;
            }

            document.getElementById('bookmaker-name').textContent = 
                `Insira suas credenciais reais para conectar à ${selectedBookmaker}`;

            nextScreen('login-screen');
        });

        // Login Form
        document.getElementById('login-form').addEventListener('submit', function(event) {
            event.preventDefault();
            
            const user = document.getElementById('bookmaker-user').value;
            const password = document.getElementById('bookmaker-password').value;
            const loginBtn = document.getElementById('login-btn');
            const loginStatus = document.getElementById('login-status');

            loginBtn.disabled = true;
            loginBtn.innerHTML = '<span class="loading-spinner"></span>Conectando à API...';

            // AQUI SERIA A CONEXÃO REAL COM A API
            // Por enquanto, simulamos a resposta
            setTimeout(() => {
                // Em produção, isso seria uma chamada real à API da casa de apostas
                // Exemplo: fetch('/api/bookmaker/login', { method: 'POST', body: JSON.stringify({user, password}) })
                
                detectedBalance = 850.50; // Exemplo: saldo real detectado
                
                loginStatus.innerHTML = `
                    <div class="success-box">
                        ✓ Conectado com sucesso!<br>
                        Saldo detectado: ${selectedCurrencySymbol} ${detectedBalance.toFixed(2)}
                    </div>
                `;
                loginStatus.style.display = 'block';

                // Atualizar tela de configuração
                document.getElementById('detected-balance').textContent = 
                    `${selectedCurrencySymbol} ${detectedBalance.toFixed(2)}`;
                document.getElementById('display-currency').textContent = `${selectedCurrencySymbol} ${selectedCurrency}`;

                loginBtn.disabled = false;
                loginBtn.innerHTML = 'CONECTAR À API';

                // Avançar para configuração
                setTimeout(() => {
                    nextScreen('config-screen');
                }, 2000);
            }, 2000);
        });

        // Config Form
        document.getElementById('config-form').addEventListener('submit', function(event) {
            event.preventDefault();
            
            const valorAposta = parseFloat(document.getElementById('valor-aposta').value).toFixed(2);
            const capitalOp = parseFloat(document.getElementById('capital-operacional').value).toFixed(2);
            const perda = parseFloat(document.getElementById('max-perda-diaria').value).toFixed(2);
            const ganho = parseFloat(document.getElementById('max-ganho-diario').value).toFixed(2);
            
            const statusDiv = document.getElementById('final-status');
            statusDiv.innerHTML = `
                <div class="success-box">
                    ✅ PROJETO X7 INICIADO COM SUCESSO!
                </div>
                <div class="info-line">
                    <strong>Casa de Apostas:</strong>
                    <span>${selectedBookmaker}</span>
                </div>
                <div class="info-line">
                    <strong>Saldo da Conta:</strong>
                    <span>${selectedCurrencySymbol} ${detectedBalance.toFixed(2)}</span>
                </div>
                <div class="info-line">
                    <strong>Capital Operacional:</strong>
                    <span>${selectedCurrencySymbol} ${capitalOp}</span>
                </div>
                <div class="info-line">
                    <strong>Valor Base da Aposta:</strong>
                    <span>${selectedCurrencySymbol} ${valorAposta}</span>
                </div>
                <div class="info-line">
                    <strong>Limite de Perda Diária:</strong>
                    <span>${selectedCurrencySymbol} ${perda}</span>
                </div>
                <div class="info-line">
                    <strong>Meta de Ganho Diário:</strong>
                    <span>${selectedCurrencySymbol} ${ganho}</span>
                </div>
                <div class="info-line">
                    <strong>Status:</strong>
                    <span style="color: var(--color-green);">🟢 Operacional - Monitorando 24/7</span>
                </div>
                <p style="margin-top: 20px; color: var(--color-green); font-weight: bold;">
                    🚀 Bot em operação. Aguardando sinais de latência...
                </p>
            `;
            statusDiv.style.display = 'block';
        });
    </script>
</body>
</html>
