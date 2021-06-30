
# spring pet client 
"spring Formatting violations found in the following files:" 

위 에러 메세지가 뜨면서 exit(1)이 발생하는 경우가 있습니다. 찾아보니 스프링 관련 문법(?)을 어겼다고 규칙에 맞는 문법으로 수정하라는 오류메세지인데, 빈 객체에서는 생성자를 통해 빈 객체를 주입받는 문법만 허용해서 발생하는 오류입니다.

"mvnw spring-javaformat:apply"


mvnw clean compile package -DskipTests=true




# Eureka

http -j GET http://100.51.0.209:8761/eureka/apps/  

 http  DELETE http://100.51.0.209:8761/eureka/apps/CONTENT-SERVICE/content-service:eef1f9d43acdf02bf665018422cc6996

 http PUT http://100.51.0.209:8761/eureka/apps/CONTENT-SERVICE/content-service:eef1f9d43acdf02bf665018422cc6996/status?value=OUT_OF_SERVICE

 http PUT http://100.51.0.209:8761/eureka/apps/CONTENT-SERVICE/content-service:eef1f9d43acdf02bf665018422cc6996/status?value=DOWN

 http PUT http://100.51.0.209:8761/eureka/apps/CONTENT-SERVICE/content-service:eef1f9d43acdf02bf665018422cc6996/status?value=UP