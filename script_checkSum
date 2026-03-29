#!/usr/bin/env bash

# ==========================================================
# Script : verifica_hash.sh
# Versão : 2.0
# Objetivo: Verificar a integridade de um arquivo
#           comparando o hash SHA-256 local com o oficial
# Uso    : ./verifica_hash.sh <arquivo> <hash_sha256_oficial>
# ==========================================================

set -euo pipefail

# --------------------------------------------------------------------------- #
# CONSTANTES
# --------------------------------------------------------------------------- #
readonly SCRIPT_NAME="$(basename "$0")"
readonly REQUIRED_TOOL="sha256sum"

# --------------------------------------------------------------------------- #
# CORES (desativadas automaticamente se não for terminal)
# --------------------------------------------------------------------------- #
if [[ -t 1 ]]; then
    GREEN='\033[0;32m'
    RED='\033[0;31m'
    YELLOW='\033[1;33m'
    CYAN='\033[0;36m'
    BOLD='\033[1m'
    RESET='\033[0m'
else
    GREEN='' RED='' YELLOW='' CYAN='' BOLD='' RESET=''
fi

# --------------------------------------------------------------------------- #
# FUNÇÕES DE LOG
# --------------------------------------------------------------------------- #
log_info()  { echo -e "${CYAN}[INFO]${RESET}  $*"; }
log_ok()    { echo -e "${GREEN}[  OK ]${RESET}  $*"; }
log_warn()  { echo -e "${YELLOW}[WARN]${RESET}  $*" >&2; }
log_error() { echo -e "${RED}[ERRO]${RESET}  $*" >&2; }

# --------------------------------------------------------------------------- #
# FUNÇÕES
# --------------------------------------------------------------------------- #

# Exibe como usar o script e encerra
usage() {
    echo -e "${BOLD}Uso:${RESET} $SCRIPT_NAME <arquivo> <hash_sha256_oficial>"
    echo
    echo "  <arquivo>            Caminho do arquivo a ser verificado"
    echo "  <hash_sha256_oficial> Hash SHA-256 esperado (64 caracteres hexadecimais)"
    echo
    echo "Exemplos:"
    echo "  $SCRIPT_NAME instalador.deb d7a8fbb307d7809469ca9abcb0082e4f8d5651e46d3cdb762d02d0bf37c9e592"
    exit 1
}

# Verifica se todas as dependências estão disponíveis
check_dependencies() {
    if ! command -v "$REQUIRED_TOOL" &>/dev/null; then
        log_error "Dependência ausente: '$REQUIRED_TOOL' não encontrado no PATH."
        exit 2
    fi
}

# Valida se o hash tem formato correto (64 hex chars)
validate_hash_format() {
    local hash="$1"
    if [[ ! "$hash" =~ ^[a-fA-F0-9]{64}$ ]]; then
        log_error "Formato de hash inválido. SHA-256 deve ter exatamente 64 caracteres hexadecimais."
        log_error "Hash recebido: '${hash}' (${#hash} caracteres)"
        exit 3
    fi
}

# Calcula o hash SHA-256 do arquivo
compute_hash() {
    local file="$1"
    sha256sum "$file" | cut -d' ' -f1
}

# Compara os hashes de forma segura (tempo constante via shell nativo)
hashes_match() {
    local hash_a="${1,,}"  # converte para minúsculas
    local hash_b="${2,,}"
    [[ "$hash_a" == "$hash_b" ]]
}

# --------------------------------------------------------------------------- #
# FLUXO PRINCIPAL
# --------------------------------------------------------------------------- #
main() {
    # 1. Verificar dependências
    check_dependencies

    # 2. Validar número de argumentos
    if [[ $# -ne 2 ]]; then
        log_error "Número de argumentos incorreto. Esperado: 2, Recebido: $#"
        echo
        usage
    fi

    local arquivo="$1"
    local hash_oficial="$2"

    # 3. Validar formato do hash oficial
    validate_hash_format "$hash_oficial"

    # 4. Verificar existência e tipo do arquivo
    if [[ ! -e "$arquivo" ]]; then
        log_error "Arquivo não encontrado: '$arquivo'"
        exit 4
    fi

    if [[ ! -f "$arquivo" ]]; then
        log_error "'$arquivo' não é um arquivo regular."
        exit 4
    fi

    if [[ ! -r "$arquivo" ]]; then
        log_error "Sem permissão de leitura para: '$arquivo'"
        exit 4
    fi

    # 5. Calcular hash local
    log_info "Calculando hash SHA-256 de '$(basename "$arquivo")'..."
    local hash_local
    hash_local="$(compute_hash "$arquivo")"

    # 6. Exibir resumo
    echo
    printf "  ${BOLD}%-14s${RESET} %s\n" "Arquivo:"   "$arquivo"
    printf "  ${BOLD}%-14s${RESET} %s\n" "Hash local:"   "${hash_local,,}"
    printf "  ${BOLD}%-14s${RESET} %s\n" "Hash oficial:" "${hash_oficial,,}"
    echo

    # 7. Comparar hashes
    if hashes_match "$hash_local" "$hash_oficial"; then
        log_ok "${GREEN}${BOLD}MATCH${RESET} — Integridade confirmada. O arquivo não foi alterado."
        exit 0
    else
        log_error "${RED}${BOLD}MISMATCH${RESET} — Arquivo corrompido ou adulterado!"
        log_warn "Não utilize este arquivo. Faça o download novamente de uma fonte confiável."
        exit 5
    fi
}

main "$@"
