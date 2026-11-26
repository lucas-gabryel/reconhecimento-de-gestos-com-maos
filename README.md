# Reconhecimento de gestos com as mãos

Projeto de Processamento de Sinais e Imagem. O notebook principal é o **`handRecongnitionV3.ipynb`**.

## Como funciona
O **MediaPipe Hands** (modelo pré-treinado do Google) encontra os 21 pontos da mão em cada quadro da webcam.
Esses pontos são normalizados (punho como origem, tamanho da mão normalizado) e viram um vetor de 63 números,
que alimenta um classificador **Random Forest** treinado em cima deles.

## Conteúdo do notebook
| Célula | O que faz |
|---|---|
| Contagem de dedos | Demo na webcam que conta os dedos levantados com regras simples sobre os pontos |
| Setup | Imports, dicionário de gestos e `extrair_pontos` (normalização dos 21 pontos) |
| Baixar | Baixa o subconjunto do HaGRID (Hugging Face) e o ASL Alphabet (Kaggle) |
| Extrair | Roda o MediaPipe nas imagens (até 400 por gesto) e monta `X`, `y` |
| Treinar | Split 80/20, aumento de dados por espelhamento, Random Forest (200 árvores), salva `modelo_gestos.joblib` |
| Câmera | Reconhecimento ao vivo; só mostra o gesto se a confiança for > 60% |

## Gestos reconhecidos
| Dataset | Classe | Texto na tela |
|---|---|---|
| HaGRID | `like` | POSITIVO |
| HaGRID | `dislike` | NEGATIVO |
| HaGRID | `peace` | PAZ E AMOR |
| HaGRID | `rock` | ROCK N ROLL |
| HaGRID | `call` | HANG LOOSE |
| HaGRID | `middle_finger` | DEDO DO MEIO |
| HaGRID | `fist`, `palm` | NENHUM (classe de rejeição) |
| ASL Alphabet | `L` | L |

Resultado no conjunto de teste: **~93% de acurácia** (F1 macro 0,93).

## Datasets
- **HaGRID (subconjunto):** https://huggingface.co/datasets/neilrigaud/hagrid-subset (~1,5 GB das pastas usadas)
- **ASL Alphabet:** https://www.kaggle.com/datasets/grassknoted/asl-alphabet (~1 GB, só a letra L é usada). Requer credenciais do Kaggle para o `kagglehub`.

Os dados são baixados em `dados/` e o modelo é salvo em `modelo_gestos.joblib`; ambos ficam fora do git (ver `.gitignore`).

## Instalação
```bash
python -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```
**Importante:** use `mediapipe==0.10.14`. As versões novas removeram a API `mp.solutions` usada no notebook.

> As células começam com uma correção de caminho via `ctypes.windll` que **só existe no Windows**
> (resolve caminhos com acento no MediaPipe). Em macOS/Linux, comente essas 3 linhas.

Rode as células em ordem: setup → baixar → extrair → treinar → câmera. Na janela da webcam, pressione **Q** para sair.

## Citação do dataset
Kapitanov et al., *HaGRID – HAnd Gesture Recognition Image Dataset* (arXiv:2206.08219). Licença CC BY-SA 4.0.
