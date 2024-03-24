# ponderada-deepracer
O código deste repositório é projetado para calcular uma recompensa (ou pontuação) para o AWS DeepRacer com base em vários parâmetros de entrada que descrevem o estado atual do veículo e do ambiente. O objetivo dessa função de recompensa é incentivar o DeepRacer a aprender comportamentos desejáveis, como manter a velocidade ideal, permanecer na pista e alinhar sua direção com a direção ideal da pista. Abaixo, cada componente da função é detalhado.

## 1. Parâmetros de Entrada
Os parâmetros escolhidos foram:

- speed: A velocidade atual do carro.
- all_wheels_on_track: Um booleano que indica se todas as rodas estão na pista.
- waypoints: Uma lista de waypoints (pontos de referência) que definem a pista.
- closest_waypoints: Um par de índices para os waypoints mais próximos ao carro, onde o primeiro é o waypoint anterior e o segundo é o próximo waypoint.
- heading: A direção de deslocamento atual do carro, em graus.
- steering_angle: O ângulo de direção atual do carro, em graus.

## 2. Pesos de Recompensa
A função define pesos para diferentes componentes da recompensa:

- speed_weight = 70
- heading_weight = 100
- steering_weight = 50
  
Esses pesos são usados para ajustar a importância relativa da velocidade, da alinhamento da direção e do ângulo de direção na recompensa total.

## 3. Recompensa por Velocidade
A recompensa por velocidade incentiva o carro a manter uma velocidade ideal. Ela é calculada como uma proporção entre a velocidade atual ao quadrado e um intervalo definido entre a velocidade mínima e máxima ao quadrado, multiplicada pelo peso da velocidade.

## 4. Penalidade por Sair da Pista
Se o carro sair da pista (all_wheels_on_track é False), a função retorna uma recompensa mínima (1e-3), desencorajando fortemente esse comportamento.

## 5. Recompensa por Alinhamento da Direção
Esta parte calcula o alinhamento da direção do carro com a direção da pista. Primeiro, calcula-se a direção da pista com base nos waypoints mais próximos e, em seguida, a diferença entre essa direção e a direção de deslocamento atual do carro (heading). A recompensa é ajustada com base na diferença de direção, incentivando o carro a se alinhar com a pista.

Para fazer esse cálculo é utilizado o arco tangente, que retorna o ângulo em radianos entre a linha definida pelos dois waypoints e o eixo x do plano cartesiano. O resultado está no intervalo de (-π, π), o que significa que pode representar direções em qualquer orientação no plano. Diferente da função math.atan, que só pode levar em conta a inclinação da linha (ou seja, delta_y/delta_x), a função math.atan2 considera tanto o numerador (delta_y) quanto o denominador (delta_x) separadamente. Isso permite que atan2 determine o quadrante correto do ângulo resultante, fornecendo uma medida de direção completa de 360° (ou 2π radianos) ao redor do círculo. A diferença absoluta entre a direção calculada da pista e a direção atual do carro (heading) é usada para avaliar o alinhamento. Se a diferença for grande, significa que o carro está mal alinhado com a direção da pista. A função ajusta essa diferença para o intervalo de 0 a 180 graus, considerando a natureza circular das direções (um desvio de 350 graus é, efetivamente, um desvio de 10 graus na direção oposta).

## 6. Recompensa por Ângulo de Direção
Semelhante à recompensa por alinhamento da direção, esta parte calcula a recompensa com base no quão bem o ângulo de direção do carro está alinhado com a diferença de direção calculada anteriormente. Isso incentiva o carro a ajustar seu ângulo de direção de forma adequada para seguir a direção ideal da pista.
