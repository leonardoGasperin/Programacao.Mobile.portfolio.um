# Calculadora IMC com Histórico e Perfil

## Introdução

Este é um projeto de estudo desenvolvido com Ionic + Vue.js para criar uma aplicação mobile que calcula o IMC (Índice de Massa Corporal), mantém um histórico dos cálculos e permite gerenciar um perfil de usuário.

### Tecnologias Utilizadas
- Ionic Framework 8.0
- Vue.js 3.3
- Capacitor 7.2
- TypeScript
- Vite

O projeto serve como base de estudo para compreender:
- Desenvolvimento mobile com Ionic
- Gerenciamento de estado com Vue.js
- Armazenamento local com Capacitor Storage
- Sistema de rotas
- Componentes UI do Ionic
- Gerenciamento de perfil de usuário

## Build & Run

### Pré-requisitos

#### Windows
1. Instale o Node.js:
```powershell
# Baixe e instale do site oficial
https://nodejs.org/
```

2. Instale o Visual Studio Code:
```powershell
# Baixe e instale do site oficial
https://code.visualstudio.com/
```

3. Instale o Ionic CLI globalmente:
```powershell
npm install -g @ionic/cli
```

#### Linux
1. Instale o Node.js:
```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs
```

2. Instale o Visual Studio Code:
```bash
sudo snap install code --classic
```

3. Instale o Ionic CLI globalmente:
```bash
sudo npm install -g @ionic/cli
```

#### macOS
1. Instale o Node.js:
```bash
# Usando Homebrew
brew install node
```

2. Instale o Visual Studio Code:
```bash
# Usando Homebrew
brew install --cask visual-studio-code
```

3. Instale o Ionic CLI globalmente:
```bash
sudo npm install -g @ionic/cli
```

### Instalação do Projeto

1. Clone o repositório:
```bash
git clone [URL_DO_REPOSITORIO]
cd [NOME_DO_PROJETO]
```

2. Instale as dependências:
```bash
npm install
```

3. Para executar em desenvolvimento:
```bash
npm run dev
```

4. Para criar build de produção:
```bash
npm run build
```

### Executando no Android

1. Adicione a plataforma Android:
```bash
ionic cap add android
```

2. Build do projeto:
```bash
ionic cap build android
```

### Executando no iOS (requer macOS)

1. Adicione a plataforma iOS:
```bash
ionic cap add ios
```

2. Build do projeto:
```bash
ionic cap build ios
```
