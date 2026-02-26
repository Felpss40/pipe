# 🛡️ VGC IPC Hijacking & Heartbeat Emulator

Este projeto é uma Prova de Conceito desenvolvida em C++ que explora vulnerabilidades na comunicação entre processos (IPC) do serviço de segurança **VGC (Vanguard Client)**. 

O código demonstra como realizar o *hijacking* (sequestro) de **Named Pipes** utilizados para a validação de integridade (*Heartbeat*) do jogo. O resultado prático desta exploração é a capacidade de **desativar completamente o anti-cheat VGC em tempo de execução e continuar jogando normalmente, sem que o jogo detecte a ausência do Vanguard.**

> **⚠️ Aviso Legal e Ético**
> 
> Este repositório foi criado estritamente para **fins educacionais e de pesquisa em Segurança da Informação (Anti-Cheat Development & IPC Security)**. O objetivo é documentar falhas arquiteturais graves em sistemas que confiam em tráfego de Named Pipes sem validação criptográfica do servidor. O uso indevido deste código é da inteira responsabilidade do utilizador.

---

## 🎯 O que este Bypass faz na prática?

O cliente do jogo depende do serviço `vgc.exe` para validar a sessão e escanear a memória. O jogo envia "pulsos" (Heartbeats) contínuos para o VGC através de um canal de comunicação local (Named Pipe) chamado `\\.\pipe\933823D3-C77B-4BAE-89D7-A92B567236BC`. Se o VGC não responder, o jogo fecha imediatamente com um erro de conexão.

**O nosso emulador atua no meio dessa comunicação (Man-in-the-Middle):**
1. Ele reinicia rapidamente os serviços do Vanguard (`vgk` e `vgc`).
2. Durante o reinício, ele rouba o endereço do Named Pipe original e cria um servidor falso no lugar.
3. Quando o jogo tenta falar com o Vanguard para confirmar se o sistema está seguro, ele acaba conectando no nosso emulador.
4. O emulador recebe os dados de validação do jogo e simplesmente os devolve (Echo), imitando o comportamento de um anti-cheat saudável.

### 🚨 O Resultado: Jogando sem o Vanguard Ativado
Como o jogo está recebendo as respostas corretas do nosso emulador falso, **o verdadeiro serviço do Vanguard (`vgc.exe`) torna-se inútil e pode ser completamente parado ou finalizado.** Isso cria um cenário de *bypass total*: o jogador pode entrar em uma partida, iniciar o emulador e, em seguida, desativar o VGC. O jogo continuará rodando perfeitamente sem nenhuma varredura de memória acontecendo em *user-mode*, deixando o sistema totalmente aberto para a injeção de ferramentas externas ou leitura de memória sem risco de detecção por parte do VGC.
