
-- =====================================================
-- PROJETO: SISTEMA DE SOLICITACOES DE MANUTENCAO
-- SECRETARIA MUNICIPAL DE OBRAS - BELA VISTA/MS
-- =====================================================

-- Habilita as chaves estrangeiras no SQLite
PRAGMA foreign_keys = ON;

-- =====================================================
-- 1. CRIACAO DAS TABELAS
-- =====================================================

CREATE TABLE IF NOT EXISTS equipamento (
    id_equipamento INTEGER PRIMARY KEY AUTOINCREMENT,
    descricao VARCHAR(100) NOT NULL,
    tipo VARCHAR(50) NOT NULL,
    identificacao VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE IF NOT EXISTS solicitacao_manutencao (
    id_solicitacao INTEGER PRIMARY KEY AUTOINCREMENT,
    id_equipamento INTEGER NOT NULL,
    descricao_problema TEXT NOT NULL,
    data_abertura DATE NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'Aberta',

    FOREIGN KEY (id_equipamento)
        REFERENCES equipamento(id_equipamento),

    CHECK (status IN (
        'Aberta',
        'Em andamento',
        'Concluida',
        'Cancelada'
    ))
);

-- =====================================================
-- 2. INSERCAO DE EQUIPAMENTOS
-- =====================================================

INSERT INTO equipamento
    (descricao, tipo, identificacao)
VALUES
    ('Caminhao basculante', 'Veiculo', 'CAM-001'),
    ('Pa-carregadeira', 'Maquina', 'MAQ-001'),
    ('Motoniveladora', 'Maquina', 'MAQ-002');

-- =====================================================
-- 3. INSERCAO DE SOLICITACOES
-- =====================================================

INSERT INTO solicitacao_manutencao
    (id_equipamento, descricao_problema, data_abertura, status)
VALUES
    (1, 'Problema no sistema de freios', '2026-10-20', 'Aberta'),
    (2, 'Vazamento de oleo hidraulico', '2026-10-21', 'Em andamento'),
    (3, 'Falha no sistema de direcao', '2026-10-22', 'Concluida');

-- =====================================================
-- 4. CONSULTA DE TODOS OS EQUIPAMENTOS
-- =====================================================

SELECT *
FROM equipamento;

-- =====================================================
-- 5. CONSULTA DAS SOLICITACOES COM EQUIPAMENTOS
-- =====================================================

SELECT
    s.id_solicitacao,
    e.descricao AS equipamento,
    e.identificacao,
    s.descricao_problema,
    s.data_abertura,
    s.status
FROM solicitacao_manutencao AS s
INNER JOIN equipamento AS e
    ON s.id_equipamento = e.id_equipamento;

-- =====================================================
-- 6. CONSULTA DE SOLICITACOES ABERTAS
-- =====================================================

SELECT *
FROM solicitacao_manutencao
WHERE status = 'Aberta';

-- =====================================================
-- 7. ATUALIZACAO DO STATUS
-- =====================================================

UPDATE solicitacao_manutencao
SET status = 'Concluida'
WHERE id_solicitacao = 1;

-- =====================================================
-- 8. ATUALIZACAO DA DESCRICAO DO EQUIPAMENTO
-- =====================================================

UPDATE equipamento
SET descricao = 'Caminhao basculante 01'
WHERE id_equipamento = 1;

-- =====================================================
-- 9. EXCLUSAO DE UMA SOLICITACAO
-- =====================================================

DELETE FROM solicitacao_manutencao
WHERE id_solicitacao = 3;

-- =====================================================
-- 10. CONSULTA DO HISTORICO DE MANUTENCAO
-- =====================================================

SELECT
    e.descricao AS equipamento,
    s.descricao_problema,
    s.data_abertura,
    s.status
FROM equipamento AS e
INNER JOIN solicitacao_manutencao AS s
    ON e.id_equipamento = s.id_equipamento
WHERE e.id_equipamento = 1
ORDER BY s.data_abertura DESC;
