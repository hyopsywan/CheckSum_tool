# 🔐 CheckSum Tool

> Ferramenta CLI em Bash para verificação de integridade de arquivos via hash **SHA-256**.  
> Ideal para validar instaladores, pacotes e qualquer arquivo antes de executá-los.

---

## 📋 Índice

- [Sobre](#-sobre)
- [Requisitos](#-requisitos)
- [Instalação](#-instalação)
- [Como usar](#-como-usar)
- [Modos de execução](#-modos-de-execução)
- [Exemplos reais](#-exemplos-reais)
- [Códigos de saída](#-códigos-de-saída)

---

## 📌 Sobre

O **CheckSum Tool** calcula o hash SHA-256 de um arquivo local e compara com o hash oficial fornecido pelo fabricante/desenvolvedor. Se os hashes forem idênticos, o arquivo está íntegro. Se divergirem, o arquivo pode estar corrompido ou adulterado.

---

## ✅ Requisitos

- Sistema Linux (testado no **Kali Linux**)
- `bash` >= 4.0
- `sha256sum` (incluso por padrão na maioria das distros)

---

## 📦 Instalação

Clone o repositório e torne o script executável:

```bash
git clone https://github.com/hyopsywan/CheckSum_tool.git
cd CheckSum_tool
chmod +x script_checkSum.sh
```

![Clone e permissão de execução](gitclone.png)

Confirme que as permissões foram aplicadas corretamente com `ls -l`:

![Tornando executável](executavel.png)

---

## 🚀 Como usar

O script suporta **3 modos de execução**:

### Modo 1 — Totalmente interativo
Roda sem argumentos. O script solicita o caminho do arquivo e o hash um a um:

```bash
./script_checkSum.sh
```

### Modo 2 — Arquivo via argumento, hash interativo
Passe o arquivo como argumento. O script solicita apenas o hash:

```bash
./script_checkSum.sh ~/Desktop/vscode.deb
```

### Modo 3 — Execução direta (sem prompts)
Passe arquivo e hash direto. Útil em pipelines e automações:

```bash
./script_checkSum.sh <arquivo> <hash_sha256>
```

---

## 🔎 Como encontrar o hash oficial de uma aplicação

Antes de verificar um arquivo, você precisa do hash oficial fornecido pelo desenvolvedor/fabricante. Veja onde procurar:

**📌 Locais mais comuns:**
- Rodapé da página de download oficial
- Página de releases no GitHub (ex: `SHA256SUMS`, `checksums.txt`)
- Documentação ou blog de atualização do software
- API pública do fabricante

### Exemplo: Visual Studio Code

No site oficial do VS Code ([code.visualstudio.com/download](https://code.visualstudio.com/download)), role até o final da página e clique em **"See SHA-256 Hashes"**:

![Rodapé da página de download do VS Code](Captura_de_tela_2026-03-29_170824.png)

Isso abre uma tabela com o hash de cada versão disponível. Para Linux .deb (x64), copie o hash correspondente:

![Tabela de hashes SHA-256 do VS Code](Captura_de_tela_2026-03-29_171208.png)

> 💡 **Dica:** Prefira sempre buscar o hash na fonte oficial. Nunca confie em hashes publicados em fóruns, sites espelho ou repositórios de terceiros.

Alternativamente, você pode obter o hash via terminal sem abrir o navegador:

```bash
curl -s "https://update.code.visualstudio.com/api/update/linux-deb-x64/stable/latest" \
  | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['sha256hash'])"
```

---

## 🧪 Exemplos reais

### Baixando o VS Code e obtendo o hash oficial

```bash
# Baixa o instalador .deb (Linux x64)
curl -L "https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-x64" \
  -o ~/Desktop/vscode.deb

# Obtém o hash SHA-256 oficial via API da Microsoft
curl -s "https://update.code.visualstudio.com/api/update/linux-deb-x64/stable/latest" \
  | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['sha256hash'])"
```

![Baixando VS Code e obtendo hash](instalandovscode.png)

---

### ✅ Resultado: MATCH — Integridade confirmada

Quando o hash local bate com o hash oficial:

```bash
./script_checkSum.sh ~/Desktop/vscode.deb
```

![Validação com sucesso - MATCH](validacaometodo2.png)

---

### ❌ Resultado: MISMATCH — Arquivo corrompido ou adulterado

Quando os hashes divergem, o script alerta e recomenda novo download:

![Validação com falha - MISMATCH](Captura_de_tela_2026-03-29_165804.png)

> ⚠️ **Nunca execute um arquivo que falhou na verificação de integridade.**

---

## 📊 Códigos de saída

| Código | Significado |
|--------|-------------|
| `0` | ✅ MATCH — integridade confirmada |
| `1` | Uso incorreto / argumentos inválidos |
| `2` | Dependência ausente (`sha256sum`) |
| `3` | Formato de hash inválido |
| `4` | Arquivo não encontrado ou sem permissão |
| `5` | ❌ MISMATCH — arquivo corrompido ou adulterado |

---

## 🛡️ Boas práticas de segurança

- Sempre obtenha o hash oficial diretamente do site do fabricante ou via API oficial
- Nunca confie em hashes publicados em fóruns ou sites de terceiros
- Em caso de MISMATCH, faça o download novamente de uma fonte confiável

---

## 👤 Autor

Desenvolvido por **hyopsywan**  
🔗 [github.com/hyopsywan](https://github.com/hyopsywan)
