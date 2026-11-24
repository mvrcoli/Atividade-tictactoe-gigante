algoritmo "JogoDaVelhaSupremo"

var
  // ---------- VETORES ----------
  tabuleiro: vetor[1..81] de caractere   // as 81 casinhas (9 tabuleiros x 9 posicoes)
  grande: vetor[1..9] de caractere       // resultado de cada tabuleiro pequeno
  linhas: vetor[1..24] de inteiro        // as 8 linhas vencedoras (3 numeros cada)

  // ---------- VARIAVEIS DO JOGO ----------
  jogador, vencedor: caractere           // quem joga agora / quem venceu a partida
  texto: caractere                       // o que o jogador digitou (lido como texto)
  casa, posicao, indice, base: inteiro   // casa = tabuleiro pequeno (1..9)
  i, j, k, c, qtd, vazias: inteiro       // contadores e auxiliares
  bloqX, bloqO: inteiro                  // quantas casas bloqueiam X / O em uma linha
  numCasa, numPos: inteiro               // usados so para DESENHAR o tabuleiro
  linhaG, linhaP, colG, colP: inteiro    // usados so para DESENHAR o tabuleiro
  ganhou, valida, terminou: logico
  vivoX, vivoO: logico                   // X / O ainda tem chance de vencer?

  // ------------------------------------------------------------
  // PROCEDIMENTO
  // ------------------------------------------------------------
procedimento mostrarTudo
  inicio
    escreval("")
    escreval("=== TABULEIRO COMPLETO (. = vazio) ===")
    para linhaG de 0 ate 2 faca
      para linhaP de 0 ate 2 faca
        para colG de 0 ate 2 faca
          numCasa <- linhaG*3 + colG + 1
          para colP de 1 ate 3 faca
            numPos <- linhaP*3 + colP
            escreva(tabuleiro[(numCasa-1)*9 + numPos], " ")
          fimpara
          escreva("  |  ")
        fimpara
        escreval("")
      fimpara
      escreval("---------------------------")
    fimpara
    escreval("PLACAR DOS TABULEIROS (E = empate):")
    para linhaG de 0 ate 2 faca
      escreva("   ")
      para colG de 0 ate 2 faca
        escreva(grande[linhaG*3 + colG + 1], " ")
      fimpara
      escreval("")
    fimpara
    escreval("")
fimprocedimento

// ------------------------------------------------------------
// PRINCIPAL
// ------------------------------------------------------------
inicio
  // ETAPA 1 - Preparar os vetores
  para i de 1 ate 81 faca
    tabuleiro[i] <- "."
  fimpara
  para i de 1 ate 9 faca
    grande[i] <- "."
  fimpara

  // Linhas vencedoras: cada grupo de 3 numeros e uma linha
  // Horizontais
  linhas[1] <- 1
  linhas[2] <- 2
  linhas[3] <- 3
  linhas[4] <- 4
  linhas[5] <- 5
  linhas[6] <- 6
  linhas[7] <- 7
  linhas[8] <- 8
  linhas[9] <- 9
  // Verticais
  linhas[10] <- 1
  linhas[11] <- 4
  linhas[12] <- 7
  linhas[13] <- 2
  linhas[14] <- 5
  linhas[15] <- 8
  linhas[16] <- 3
  linhas[17] <- 6
  linhas[18] <- 9
  // Diagonais
  linhas[19] <- 1
  linhas[20] <- 5
  linhas[21] <- 9
  linhas[22] <- 3
  linhas[23] <- 5
  linhas[24] <- 7

  jogador <- "X"
  vencedor <- "."
  terminou <- falso

  escreval("======================================")
  escreval("      JOGO DA VELHA SUPREMO")
  escreval("======================================")
  escreval("Numeracao dos tabuleiros e das posicoes:")
  escreval("   1 2 3")
  escreval("   4 5 6")
  escreval("   7 8 9")
  escreval("Vence quem ganhar 3 tabuleiros em linha.")

  // ETAPA 2 - Laco principal: cada volta e uma jogada
  enquanto nao terminou faca
    mostrarTudo
    escreval("Vez do jogador ", jogador)

    // ETAPA 3 - Validar o tabuleiro escolhido
    repita
      valida <- verdadeiro
      escreva("Escolha o tabuleiro (1 a 9): ")
      leia(texto)
      se (compr(texto) <> 1) ou (texto < "1") ou (texto > "9") entao
        escreval("Numero invalido. Digite de 1 a 9.")
        valida <- falso
      senao
        casa <- caracpnum(texto)
        se grande[casa] <> "." entao
          escreval("Esse tabuleiro ja foi encerrado. Escolha outro.")
          valida <- falso
        fimse
      fimse
    ate valida

    // ETAPA 4 - Validar a posicao escolhida
    repita
      valida <- verdadeiro
      escreva("Escolha a posicao (1 a 9) dentro do tabuleiro ", casa, ": ")
      leia(texto)
      se (compr(texto) <> 1) ou (texto < "1") ou (texto > "9") entao
        escreval("Numero invalido. Digite de 1 a 9.")
        valida <- falso
      senao
        posicao <- caracpnum(texto)
        indice <- (casa-1)*9 + posicao
        se tabuleiro[indice] <> "." entao
          escreval("Essa posicao ja esta ocupada.")
          valida <- falso
        fimse
      fimse
    ate valida

    // ETAPA 5 - Registrar a jogada
    base <- (casa-1)*9
    indice <- base + posicao
    tabuleiro[indice] <- jogador

    // ETAPA 6 - Vitoria no tabuleiro PEQUENO
    ganhou <- falso
    para k de 1 ate 8 faca
      qtd <- 0
      para j de 1 ate 3 faca
        c <- linhas[(k-1)*3 + j]
        se tabuleiro[base + c] = jogador entao
          qtd <- qtd + 1
        fimse
      fimpara
      se qtd = 3 entao
        ganhou <- verdadeiro
      fimse
    fimpara

    se ganhou entao
      grande[casa] <- jogador
      escreval(">>> O jogador ", jogador, " conquistou o tabuleiro ", casa, "!")
    senao
      // ETAPA 7 - Empate no tabuleiro pequeno (nao sobrou casa vazia)
      vazias <- 0
      para i de 1 ate 9 faca
        se tabuleiro[base + i] = "." entao
          vazias <- vazias + 1
        fimse
      fimpara
      se vazias = 0 entao
        grande[casa] <- "E"
        escreval(">>> O tabuleiro ", casa, " empatou e foi encerrado.")
      fimse
    fimse

    // ETAPA 8 - Vitoria no tabuleiro PRINCIPAL
    para k de 1 ate 8 faca
      qtd <- 0
      para j de 1 ate 3 faca
        c <- linhas[(k-1)*3 + j]
        se grande[c] = jogador entao
          qtd <- qtd + 1
        fimse
      fimpara
      se qtd = 3 entao
        vencedor <- jogador
        terminou <- verdadeiro
      fimse
    fimpara

    // ETAPA 9 - Ainda existe possibilidade de vitoria?
    se nao terminou entao
      vivoX <- falso
      vivoO <- falso
      para k de 1 ate 8 faca
        bloqX <- 0
        bloqO <- 0
        para j de 1 ate 3 faca
          c <- linhas[(k-1)*3 + j]
          se (grande[c] = "O") ou (grande[c] = "E") entao
            bloqX <- bloqX + 1
          fimse
          se (grande[c] = "X") ou (grande[c] = "E") entao
            bloqO <- bloqO + 1
          fimse
        fimpara
        se bloqX = 0 entao
          vivoX <- verdadeiro
        fimse
        se bloqO = 0 entao
          vivoO <- verdadeiro
        fimse
      fimpara
      se (nao vivoX) e (nao vivoO) entao
        terminou <- verdadeiro
      fimse
    fimse

    // ETAPA 10 - Passar a vez
    se nao terminou entao
      se jogador = "X" entao
        jogador <- "O"
      senao
        jogador <- "X"
      fimse
    fimse
  fimenquanto

  // ETAPA 11 - Resultado final
  mostrarTudo
  se vencedor <> "." entao
    escreval("FIM DE JOGO! O jogador ", vencedor, " venceu a partida!")
  senao
    escreval("FIM DE JOGO! VELHA: ninguem tem mais como vencer.")
  fimse
fimalgoritmo

