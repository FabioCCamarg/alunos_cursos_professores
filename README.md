# EXERCÍCIOS BANCO DE DADOS  
## Descrição

/*Crie um banco de dados para armazenar alunos, cursos e professores de uma
universidade;*/

/*Criando banco de dados aulabd.*/

	CREATE SCHEMA IF NOT EXISTS `aulabd` DEFAULT CHARACTER SET utf8 ;
	USE `aulabd` ;

/*Criando Tabela Professores.*/

	CREATE TABLE IF NOT EXISTS `aulabd`.`professores` (
	`id` INT NOT NULL AUTO_INCREMENT,
	`nome_professor` VARCHAR(45) NOT NULL,
	`diciplina` VARCHAR(45) NULL,
	PRIMARY KEY (`id`))
	ENGINE = InnoDB;

/*Criando Tabela Cursos*/

	CREATE TABLE IF NOT EXISTS `aulabd`.`cursos` (
	`id` INT NOT NULL AUTO_INCREMENT,
	`nome_curso` VARCHAR(45) NOT NULL,
	`qt_horas` INT NOT NULL,
	PRIMARY KEY (`id`))
	ENGINE = InnoDB;

/*Criando Tabela Alunos.*/

	CREATE TABLE IF NOT EXISTS `aulabd`.`alunos` (
	`id` INT NOT NULL AUTO_INCREMENT,
	`nome` VARCHAR(45) NOT NULL,
	`email` VARCHAR(45) NOT NULL,
	`idade` INT NOT NULL,
	`professores_id` INT NOT NULL,
	`cursos_id` INT NOT NULL,
	PRIMARY KEY (`id`, `professores_id`, `cursos_id`),
	INDEX `fk_alunos_professores1_idx` (`professores_id` ASC) VISIBLE,
	INDEX `fk_alunos_cursos1_idx` (`cursos_id` ASC) VISIBLE,
	CONSTRAINT `fk_alunos_professores1`
		FOREIGN KEY (`professores_id`)
		REFERENCES `aulabd`.`professores` (`id`)
		ON DELETE NO ACTION
		ON UPDATE NO ACTION,
	CONSTRAINT `fk_alunos_cursos1`
		FOREIGN KEY (`cursos_id`)
		REFERENCES `aulabd`.`cursos` (`id`)
		ON DELETE NO ACTION
		ON UPDATE NO ACTION)
		ENGINE = InnoDB;

/*Criando Tabela Professores_Cursos.*/

	CREATE TABLE IF NOT EXISTS `aulabd`.`professores_cursos` (
	`professores_id` INT NOT NULL,
	`cursos_id` INT NOT NULL,
	PRIMARY KEY (`professores_id`, `cursos_id`),
	INDEX `fk_professores_cursos_cursos1_idx` (`cursos_id` ASC) VISIBLE,
	INDEX `fk_professores_cursos_professores1_idx` (`professores_id` ASC) VISIBLE,
	CONSTRAINT `fk_professores_cursos_professores1`
		FOREIGN KEY (`professores_id`)
		REFERENCES `aulabd`.`professores` (`id`)
		ON DELETE NO ACTION
		ON UPDATE NO ACTION,
	CONSTRAINT `fk_professores_cursos_cursos1`
		FOREIGN KEY (`cursos_id`)
		REFERENCES `aulabd`.`cursos` (`id`)
		ON DELETE NO ACTION
		ON UPDATE NO ACTION)
	ENGINE = InnoDB;

/*Faça a modelagem do banco e identifique as entidades, seus atributos e relacionamentos;(imagem)*/

![exer1](https://raw.githubusercontent.com/FabioCCamarg/alunos_cursos_professores/main/alunos_cursos_professores.png)

/*Registros para Tabela Alunos;*/

    insert into alunos (nome, email, idade, professores_id, cursos_id) values('Fabio','fabio.costa@gmail.com', 14,1,1);
    insert into alunos (nome, email, idade, professores_id, cursos_id) values('Eric','eric.gera@gmail.com', 16, 2,2);

/*Registros para Tabela Cursos*/

    insert into cursos (nome_curso, qt_horas) values ('Fisica', 20);
    insert into cursos (nome_curso, qt_horas) values ('Ciencias', 10);

/*Registros para Tabela Professores*/

    insert into  professores  (nome_professor, diciplina) values ('Cristian','Matematica');
    insert into  professores  (nome_professor, diciplina) values ('Alan','Educacao fisica');


/*Utilize Stored Procedures para automatizar a inserção e seleção dos cursos;*/

/* CRIANDO STORE PROCEDURE PRA INSERIR CURSOS*/

    DELIMITER //
    CREATE PROCEDURE insercao_de_Curso (
      IN nome_curso VARCHAR(45),
      IN qt_horas INT
    )
    BEGIN
      INSERT INTO cursos (nome_curso, qt_horas) VALUES (nome_curso, qt_horas);
    END//
    DELIMITER ;

/*utlizando a store procedure já criada para inserir os cursos.*/

    CALL insercao_de_Curso('Ingles', 50);
    CALL insercao_de_Curso('Ginastica', 20);

/*CRIANDO A STORE PROCEDURE PARA SELECIONAR TODOS OS CURSOS.*/

    DELIMITER //
    CREATE PROCEDURE seleciona_curso ()
    BEGIN
	      SELECT * FROM cursos;
    END //
    DELIMITER ;

/*utlizando a store procedure para trazer os cursos.*/

    CALL seleciona_curso ();

/*O aluno possui um email que deve ter seu endereço gerado automaticamente no seguinte
formato:
nome.sobrenome@dominio.com*/

    DELIMITER //

    CREATE PROCEDURE inserir_email_automatico(
        IN nome VARCHAR(45),
        IN sobrenome VARCHAR(45),
        IN idade INT,
        IN professores_id INT,
        IN cursos_id INT
    )
    BEGIN
        SET @email = CONCAT(nome, '.', sobrenome, '@gmail.com');
    
        INSERT INTO alunos (nome, sobrenome, idade, professores_id, cursos_id, email)
        VALUES (nome, sobrenome, idade, professores_id, cursos_id, @email);
    END//

    DELIMITER ;

    CALL inserir_email_automatico ('varistotes','gama', 15, 4, 2);

/*Como fica o email se duas pessoas tiverem o mesmo nome e sobrenome?*/
/*foi inserido mais um registro para passar configurar o email para diferenciar de uma pessoa com mesmo sobrenome.*/

    DELIMITER //

    CREATE PROCEDURE inserir_email_automatico_exclusivo(
        IN nome VARCHAR(45),
        IN sobrenome VARCHAR(45),
        IN idade INT,
        IN professores_id INT,
        IN cursos_id INT
    )
    BEGIN
        SET @email = CONCAT(nome, '.', sobrenome,'-',idade, '@gmail.com');
    
        INSERT INTO alunos (nome, sobrenome, idade, professores_id, cursos_id, email)
        VALUES (nome, sobrenome, idade, professores_id, cursos_id, @email);
    END//

    DELIMITER ;

    CALL inserir_email_automatico_exclusivo ('joao','siqueira', 98, 2, 1);
