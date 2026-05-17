-- Consulta fictícia utilizada apenas para demonstração de portfólio
-- Os nomes das tabelas e campos foram adaptados

SELECT
    s.id_servico,
    v.placa,
    v.modelo_veiculo,
    v.categoria,
    c.nome_contrato,
    e.nome_empresa,
    s.data_servico,
    s.status_servico,
    s.km_atual,
    s.valor_total
FROM servicos_manutencao s
LEFT JOIN veiculos v 
    ON s.id_veiculo = v.id_veiculo
LEFT JOIN contratos c 
    ON v.id_contrato = c.id_contrato
LEFT JOIN empresas e 
    ON c.id_empresa = e.id_empresa
WHERE s.data_servico >= '2025-01-01';