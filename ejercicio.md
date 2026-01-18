-- Pregunta 1
-- ¿Quien es el actor o actriz que ha participado en la mayor cantidad de series?
SELECT actor_id  , COUNT(actor_id) as cantActuaciones FROM `Actuaciones` 
GROUP by actor_id
ORDER BY cantActuaciones DESC;
//
respuesta Gina Carano

-- Pregunta 2
-- ¿Cual es la serie con mejor rating promedio segun imdb?


-- Pregunta 3
-- ¿Cual es el episodio con la duración más larga?
