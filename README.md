# Parte-2-Clinica-Vet


CREATE TABLE Tratamentos (
    id_tratamento INT PRIMARY KEY AUTO_INCREMENT,
    id_paciente INT,
    id_veterinario INT,
    data_tratamento DATE,
    descricao VARCHAR(255),
    custo DECIMAL(10, 2),
    FOREIGN KEY (id_paciente) REFERENCES Pacientes(id_paciente),
    FOREIGN KEY (id_veterinario) REFERENCES Veterinarios(id_veterinario)
);

DELIMITER //
CREATE PROCEDURE agendar_tratamento (
    IN id_paciente INT,
    IN id_veterinario INT,
    IN data_tratamento DATE,
    IN descricao VARCHAR(255),
    IN custo DECIMAL(10, 2)
)
BEGIN
    INSERT INTO Tratamentos (id_paciente, id_veterinario, data_tratamento, descricao, custo)
    VALUES (id_paciente, id_veterinario, data_tratamento, descricao, custo);
END //
DELIMITER ;

DELIMITER //
CREATE PROCEDURE atualizar_tratamento (
    IN id_tratamento INT,
    IN nova_descricao VARCHAR(255),
    IN novo_custo DECIMAL(10, 2)
)
BEGIN
    UPDATE Tratamentos
    SET descricao = nova_descricao,
        custo = novo_custo
    WHERE id_tratamento = id_tratamento;
END //
DELIMITER ;

DELIMITER //
CREATE PROCEDURE remover_tratamento (
    IN id_tratamento INT
)
BEGIN
    DELETE FROM Tratamentos
    WHERE id_tratamento = id_tratamento;
END //
DELIMITER ;

DELIMITER //
CREATE FUNCTION total_gasto_tratamentos(id_paciente INT) 
RETURNS DECIMAL(10, 2)
BEGIN
    DECLARE total DECIMAL(10, 2);
    SELECT COALESCE(SUM(custo), 0) INTO total
    FROM Tratamentos
    WHERE id_paciente = id_paciente;
    RETURN total;
END //
DELIMITER ;

DELIMITER //
CREATE TRIGGER verificar_custo_tratamento
BEFORE INSERT ON Tratamentos
FOR EACH ROW
BEGIN
    IF NEW.custo < 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Custo inválido: deve ser um número positivo.';
    END IF;
END //
DELIMITER ;

CALL agendar_tratamento(1, 2, '2024-09-21', 'Vacinação', 150.00);
CALL atualizar_tratamento(1, 'Vacinação + Reavaliação', 200.00);
CALL remover_tratamento(1);
SELECT total_gasto_tratamentos(1) AS gasto_total_tratamentos;
