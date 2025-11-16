🚀 Projeto DSList – Intensivão Java Spring

Repositório oficial do projeto desenvolvido no Intensivão Java Spring – DevSuperior.
Aqui você encontra materiais, código, scripts e configs utilizados durante o treinamento.

📌 1. Perdeu alguma aula ou material de apoio?

Inscreva-se no treinamento para receber os conteúdos por e-mail:

👉 https://devsuperior.com.br

⚠️ Atenção: os conteúdos ficam disponíveis apenas até domingo.
Organize-se e bora pra cima!

❓ 2. Tem alguma dúvida?

Envie sua pergunta para o e-mail que chegou para você no momento da inscrição.

🗓️ Calendário do Intensivão

As aulas ficam disponíveis no site e são liberadas sempre por volta das 9h da manhã, somente para inscritos.

Dia / horário	Conteúdo
Segunda-feira 9h	Aula 1: Projeto estruturado
Terça-feira 9h	Aula 2: Domínio e consultas
Quarta-feira 9h	Aula 3: Homologação e CORS
Quinta-feira 9h	Aula 4: Endpoint especial
Sexta-feira 9h	Aula 5: Resumão e reforço
🧩 Modelo de domínio – DSList

(Adicione a imagem do modelo aqui caso deseje)

📦 Trechos de código úteis
🔧 Plug-in Maven
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-resources-plugin</artifactId>
	<version>3.1.0</version> <!--$NO-MVN-MAN-VER$ -->
</plugin>

⚙️ Arquivos de configuração
application.properties
spring.profiles.active=${APP_PROFILE:test}
spring.jpa.open-in-view=false

cors.origins=${CORS_ORIGINS:http://localhost:5173,http://localhost:3000}

application-test.properties
# H2 Connection
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# H2 Client
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# Show SQL
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

application-dev.properties
#spring.jpa.properties.jakarta.persistence.schema-generation.create-source=metadata
#spring.jpa.properties.jakarta.persistence.schema-generation.scripts.action=create
#spring.jpa.properties.jakarta.persistence.schema-generation.scripts.create-target=create.sql
#spring.jpa.properties.hibernate.hbm2ddl.delimiter=;

spring.datasource.url=jdbc:postgresql://localhost:5432/dscatalog
spring.datasource.username=postgres
spring.datasource.password=1234567

spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none

application-prod.properties
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none

system.properties
java.runtime.version=17

🌐 WebConfig – CORS
@Configuration
public class WebConfig {

	@Value("${cors.origins}")
	private String corsOrigins;
	
	@Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/**").allowedMethods("*").allowedOrigins(corsOrigins);
			}
		};
	}
}

📚 Repositórios
GameRepository
@Query(nativeQuery = true, value = """
		SELECT tb_game.id, tb_game.title, tb_game.game_year AS `year`, tb_game.img_url AS imgUrl,
		tb_game.short_description AS shortDescription, tb_belonging.position
		FROM tb_game
		INNER JOIN tb_belonging ON tb_game.id = tb_belonging.game_id
		WHERE tb_belonging.list_id = :listId
		ORDER BY tb_belonging.position
			""")
List<GameMinProjection> searchByList(Long listId);

GameListRepository
@Modifying
@Query(nativeQuery = true, value = 
	"UPDATE tb_belonging SET position = :newPosition WHERE list_id = :listId AND game_id = :gameId")
void updateBelongingPosition(Long listId, Long gameId, Integer newPosition);

🗃️ import.sql (seeding dos dados)

Todos os inserts enviados foram organizados aqui:

INSERT INTO tb_game_list (name) VALUES ('Aventura e RPG');
INSERT INTO tb_game_list (name) VALUES ('Jogos de plataforma');

-- Jogos
INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) 
VALUES 
('Mass Effect Trilogy', 4.8, 2012, 'Role-playing (RPG), Shooter', 'XBox, Playstation, PC',
'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/1.png',
'Lorem ipsum dolor sit amet consectetur adipisicing elit. Odit esse officiis corrupti...',
'Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus dolorum illum...'),

('Red Dead Redemption 2', 4.7, 2018, 'Role-playing (RPG), Adventure', 'XBox, Playstation, PC',
'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/2.png',
'Lorem ipsum dolor sit amet consectetur...',
'Lorem ipsum dolor sit amet consectetur...'),

... *(demais inserts mantidos exatamente como enviados)*

-- Relação list × game
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 1, 0);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 2, 1);
...


(Se quiser, posso gerar o arquivo import.sql completo formatado.)

🐳 Script Docker Compose

Link oficial:

👉 https://gist.github.com/acenelio/5e40b27cfc40151e36beec1e27c4ff71
