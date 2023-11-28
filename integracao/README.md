Backend (Spring Boot)
Acesse o Spring Initializer.
Preencha as informações do projeto:
Project: Gradle Project
Atifact: Integracao
Language: Java
Spring Boot: 3.1.5
Packaging: Jar
Java: 17
Dependencies: Lombok, Spring Web, Spring Data JPA, PostgreSQL Driver.
Clique em "Generate" e baixe o arquivo zip.
Estrutura do Projeto
Extrair o conteúdo do arquivo zip e abrir o projeto no Apache NetBeans.
Crie as seguintes Classes e seus respectivos pacotes:
Model
// Usuario.java
package com.example.integracao.model;

import lombok.Getter;
import lombok.Setter;
import jakarta.persistence.*;

@Entity
@Getter
@Setter
public class Usuario {
 @Id
 @GeneratedValue(strategy = GenerationType.IDENTITY)
 private long cod;
 private String username;
 private String password;
}
@Entity: Indica que a classe é uma entidade JPA, ou seja, será mapeada para uma tabela no banco de dados.
@Id: Marca o campo cod como a chave primária da entidade.
@GeneratedValue: Define a estratégia de geração de valores automáticos para a chave primária.
@Getter e @Setter: Anotações do Lombok para gerar automaticamente métodos getter e setter.


Repository
// UsuarioRepository.java
package com.example.integracao.repository;

import com.example.integracao.model.Usuario;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UsuarioRepository extends JpaRepository<Usuario, Long> {
}
@Repository: Indica que a interface é um repositório, permitindo operações CRUD no banco de dados para a entidade Usuario.
JpaRepository<Usuario, Long>: Interface do Spring Data JPA que fornece métodos CRUD padrão para a entidade Usuario.

Controller
// AuthController.java
package com.example.integracao.controller;

import com.example.integracao.model.Usuario;
import com.example.integracao.repository.UsuarioRepository;
import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class AuthController {

 private final UsuarioRepository usuarioRepository;

 @Autowired
 public AuthController(UsuarioRepository usuarioRepository) {
 this.usuarioRepository = usuarioRepository;
 }

 @PostMapping("/login")
 public Boolean login(@RequestBody Usuario usuario) {
 List<Usuario> usuarios = usuarioRepository.findAll();
 for (Usuario u : usuarios) {
 if (u.getUsername().equals(usuario.getUsername()) && u.getPassword().equals(usuario.getPassword())) {
 return true;
 }
 }
 return false;
 }
}
@RestController: Indica que a classe é um controlador Spring que manipula solicitações HTTP e retorna respostas JSON.
@Autowired: Injeta automaticamente uma instância de UsuarioRepository no construtor.
@PostMapping("/login"): Mapeia a solicitação POST para /login.
A função login verifica se um usuário com o nome de usuário e senha fornecidos existe no banco de dados. Se encontrar, retorna true; caso contrário, retorna false.


Config
// CorsConfig.java
package com.example.integracao.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

@Configuration
public class CorsConfig {
    @Bean
public CorsFilter corsFilter() {
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowCredentials(true);
    config.addAllowedOrigin("http://localhost:5173");
    config.addAllowedHeader("*");
    config.addAllowedMethod("*");
    source.registerCorsConfiguration("/**", config);
    return new CorsFilter(source);
}

}

Esta classe é responsável por configurar o suporte a Cross-Origin Resource Sharing (CORS). CORS é uma política de segurança que restringe como os recursos em uma página web podem ser solicitados a partir de outro domínio fora do domínio que serviu a página.

@Configuration: Indica que a classe é uma classe de configuração Spring.
@Bean: Indica que o método corsFilter produz um bean que será gerenciado pelo container Spring.
CorsFilter: Um filtro que intercepta solicitações HTTP para fornecer configuração CORS.
Dentro do método corsFilter:

UrlBasedCorsConfigurationSource: Uma implementação de CorsConfigurationSource que mapeia configurações CORS a URLs específicas.
CorsConfiguration: Configuração CORS específica, permitindo credenciais, origens, cabeçalhos e métodos específicos.
config.setAllowCredentials(true): Permite o compartilhamento de credenciais, como cookies, entre origens.
config.addAllowedOrigin("http://localhost:5173"): Adiciona origens permitidas para solicitações CORS.
config.addAllowedHeader("*"): Permite qualquer cabeçalho nas solicitações.
config.addAllowedMethod("*"): Permite qualquer método HTTP nas solicitações.
source.registerCorsConfiguration("/login/**", config): Registra a configuração CORS específica para o caminho /login.


Application Properties
# src/main/resources/application.properties
server.port=8090
spring.datasource.url=jdbc:postgresql://localhost:5432/nome_do_banco
spring.datasource.username=postgres
spring.datasource.password=senai
spring.datasource.driver-class-name=org.postgresql.Driver
spring.jpa.hibernate.ddl-auto=update

Configurações para o Spring Boot, incluindo a porta do servidor, URL do banco de dados PostgreSQL, usuário, senha e estratégia de atualização do Hibernate.
Frontend (Vite)
Abra o VSCode e crie um novo projeto usando o Vite.
npm create vite loginsystem --template react
cd loginsystem 
npm install

Estrutura do Projeto
// src/App.jsx
import React, { useState } from 'react';
import axios from 'axios';

const logar = async (username, password) => {
 try {
 const response = await axios.post('http://localhost:8090/api/login', {
 username: username,
 password: password,
 });
 return response.data;
 } catch (error) {
 throw error;
 }
};
function App() {
 const [username, setUsername] = useState('');
 const [password, setPassword] = useState('');
 const handleLogin = async () => {
 try {
 const response = await logar(username, password);
 alert(response);
 } catch (error) {
 console.error('Erro ao se logar:', error);
 }
 };
 return (
 <div>
 <h1>TrêsáNo login SyStem</h1>
 <form>
 <label>
 Usuário:
 <input
 type="text"
 value={username}
 onChange={(e) => setUsername(e.target.value)}
 />
 </label>
 <br />
 <label>
 Senha:
 <input
 type="password"
 value={password}
 onChange={(e) => setPassword(e.target.value)}
 />
 </label>
 <br />
 <button type="button" onClick={handleLogin}>
 Login
 </button>
 </form>
 </div>
 );
}
export default App;

useState: Um gancho do React usado para criar variáveis de estado (username e password).
logar(username, password): Função assíncrona que envia uma solicitação POST para o endpoint de login do backend.
handleLogin(): Função que chama logar ao clicar no botão de login.
O formulário captura o nome de usuário e a senha, que são usados na função logar.

Execução do Projeto
Inicie o PostgreSQL e verifique se o banco de dados está configurado corretamente;
Inicie o backend (Spring Boot) no Apache NetBeans ou usando o comando ./gradlew bootRun;
Inicie o frontend (Vite/React) usando o comando npm run dev na pasta do frontend (loginsystem).
Acesse o aplicativo em um navegador, geralmente em http://localhost:3000.

Insira um usuário no banco de dados, com o comando:
Insert into usuario (username, password) values ('SeuNome','SeuSobrenome');
No meu Caso, ficaria assim:
Insert into usuario (username, password) values (Jackson,Wrublak);
Tente se logar utilizando o Front-End.
