**O projeto foi desenvolvido por Ariadne Evangelista e Arthur Queiroz**  
**DCA3702 – Algoritmos e Estruturas de Dados II**

# Projeto 3: Método A + MST para Dimensionamento de Infraestrutura Viária entre Pontos de Interesse
**Estudo de Caso em 8 Cidades**

**Mapeamento da Rede de Colaboração Docente da UFRN**

---

## 1. Introdução

<p align = "justify">Este trabalho tem como objetivo analisar a conectividade entre Pontos de Interesse (POIs) em diversas cidades brasileiras, estimando a extensão mínima de vias urbanas necessárias para interligá-los eficientemente. Para alcançar este objetivo, utilizamos uma metodologia que combina a modelagem da rede viária real das cidades através da biblioteca OSMnx com a aplicação de algoritmos de grafos clássicos: o algoritmo A* para encontrar os caminhos mais curtos entre pares de POIs, e o algoritmo de Kruskal para calcular a Árvore Geradora Mínima (MST) sobre o grafo completo formado por esses POIs.</p>

<p align = "justify">A abordagem envolve a seleção de um conjunto de POIs em cada cidade, a identificação dos nós mais próximos na rede viária, o cálculo das rotas mais curtas e seus respectivos comprimentos utilizando A*, a construção de um grafo de interesse onde os POIs são vértices e as arestas representam as distâncias A*, e finalmente, a aplicação do algoritmo de Kruskal para determinar a MST. A soma dos pesos das arestas da MST fornece uma estimativa do comprimento total mínimo necessário para conectar todos os POIs. Além disso, mapeamos as arestas da MST de volta para as rotas reais na malha viária para calcular o comprimento real da infraestrutura utilizada.</p>

<p align = "justify">Realizamos este estudo em pelo menos oito cidades com características geográficas e urbanísticas distintas, incluindo Natal, Palmas, uma área da Zona Norte de São Paulo, Parintins, Florianópolis, Ouro Preto, Juiz de Fora e Santos. A análise comparativa dos resultados, focando no comprimento total da MST e métricas por POI ou aresta da MST, permite investigar como fatores como o planejamento urbano, a topografia e a distribuição espacial dos POIs influenciam a eficiência da conectividade urbana. As visualizações das rotas da MST em cada cidade complementam a análise quantitativa, oferecendo uma perspectiva espacial das conexões identificadas. Este estudo busca fornecer insights sobre os desafios e padrões de conectividade em diferentes contextos urbanos brasileiros, destacando a aplicabilidade de algoritmos de grafos na análise de infraestrutura urbana.</p>

## 2. Desenvolvimento

<p align = "justify">Neste estudo, os Pontos de Interesse (POIs) selecionados foram as agências bancárias `(amenity: bank)`. A escolha se justifica pela ampla distribuição desses serviços em áreas urbanas e semiurbanas, além de sua relevância para o cotidiano da população, exigindo boa conectividade territorial. Esta abordagem oferece uma nova perspectiva sobre a conectividade urbana, centrada em serviços financeiros de acesso amplo e essencial.</p>

<p align = "justify">A escolha das oito cidades analisadas foi guiada pela busca por diversidade geográfica, demográfica e urbanística no contexto brasileiro, com o objetivo de representar distintos padrões de desenvolvimento urbano e suas implicações na conectividade territorial. As cidades selecionadas foram:</p>

- **Natal (RN):** Capital nordestina de médio porte, com urbanização consolidada e forte influência litorânea na configuração urbana.  
- **Palmas (TO):** Capital planejada mais jovem do Brasil, exemplo de urbanização moderna e traçado ortogonal.  
- **São Paulo – Zona Norte (SP):** Recorte da maior metrópole brasileira, selecionado para representar a complexidade e fragmentação urbana em uma área delimitada. A escolha por analisar apenas essa zona se deve ao elevado número de POIs e ao tempo de processamento necessário, tornando inviável a análise do município como um todo. Optou-se pela Zona Norte por ser a segunda mais populosa da cidade, permitindo uma amostra representativa dos desafios de conectividade em grandes centros urbanos. 
- **Parintins (AM):** Cidade insular na Amazônia, marcada por isolamento geográfico e infraestrutura viária singular, em contraste com centros urbanos consolidados.  
- **Florianópolis (SC):** Capital insular do Sul, com desafios de conexão entre ilha e continente e distribuição dispersa de POIs.  
- **Ouro Preto (MG):** Cidade histórica com relevo acidentado e urbanização influenciada pelo patrimônio, gerando obstáculos à conectividade.  
- **Juiz de Fora (MG):** Município de médio porte em região montanhosa, com crescimento orgânico e múltiplos polos urbanos que dificultam a interligação viária.  
- **Santos (SP):** Cidade portuária e litorânea com urbanização densa e geografia costeira que impacta a estrutura da rede viária.

<p align = "justify">A diversidade na seleção das cidades permite comparar como diferentes fatores, como o planejamento urbano (Palmas vs. Juiz de Fora), a topografia (Ouro Preto, Juiz de Fora), a geografia (Parintins, Florianópolis, Natal, Santos) e a escala urbana (São Paulo vs. Parintins), influenciam as métricas da Árvore Geradora Mínima (MST) e, por consequência, a estimativa de infraestrutura viária necessária para conectar os pontos de interesse. A análise das agências bancárias como POIs em cada um desses cenários urbanos oferece uma base sólida para discutir as disparidades regionais na acessibilidade a serviços essenciais por meio da malha viária.</p>

### 2.1 Implementação do Algoritmo

<p align = "justify">Para a implementação do projeto, foram utilizadas bibliotecas amplamente reconhecidas na análise de redes urbanas e geoprocessamento. A biblioteca OSMnx foi empregada para o download e manipulação da rede viária e dos pontos de interesse (POIs) a partir do OpenStreetMap. Já a NetworkX foi essencial para a construção e análise dos grafos, incluindo o cálculo de caminhos mínimos e da Árvore Geradora Mínima (MST). A visualização dos dados e das redes foi realizada com o auxílio da Matplotlib (import matplotlib.pyplot as plt), permitindo representar graficamente os resultados obtidos. Por fim, a biblioteca Shapely foi utilizada para manipulação de geometrias espaciais, como polígonos de delimitação urbana e coordenadas geográficas dos POIs.</p>

```
import osmnx as ox
import networkx as nx
import matplotlib.pyplot as plt
import shapely.geometry
```
### 2.2 Codificação das Funções

<p align = "justify">Para facilitar o processamento das oito cidades selecionadas, foram desenvolvidas funções específicas.</p>

<p align = "justify">A função to_undirected_multigraph(G) transforma um grafo direcionado (MultiDiGraph) em não-direcionado (MultiGraph), copiando os nós e convertendo as arestas para formato bidirecional, mantendo seus atributos. Isso é útil para análises como a Árvore Geradora Mínima (MST), que exigem grafos não-direcionados.</p>

<p align = "justify">Já get_graph_pois_nodes(place, tags) utiliza a biblioteca OSMnx para baixar a rede de ruas e os pontos de interesse (POIs) de uma área definida por nome ou polígono, como nas zonas de São Paulo e Rio de Janeiro. Os POIs são filtrados pelas tags fornecidas (ex.: BANCOS). A função converte o grafo para não-direcionado, extrai as coordenadas dos POIs, encontra os nós mais próximos, remove duplicatas e retorna: o grafo original, o grafo convertido, as coordenadas dos POIs e os nós únicos correspondentes.</p>

```
def to_undirected_multigraph(G):
    H = nx.MultiGraph()
    # Copiar nós e seus atributos
    for n, data in G.nodes(data=True):
        H.add_node(n, **data)

    # Copiar arestas e seus atributos, sem direcionamento
    for u, v, data in G.edges(data=True):
        # Em um MultiGraph, se já existir uma aresta u-v, esta será adicionada como mais uma aresta paralela
        H.add_edge(u, v, **data)

    # Copiar atributos do grafo
    H.graph.update(G.graph)
    return H

def get_graph_pois_nodes(place, tags):

    # Remover esta linha para evitar a impressão duplicada
    # print(f" >> Processando {place}...")

    # Baixar o grafo da rede de ruas
    if isinstance(place, str):
        G = ox.graph_from_place(place, network_type='drive')
        pois = ox.features.features_from_place(place, tags=tags)
    else: # Assume it's a polygon
        G = ox.graph_from_polygon(place, network_type='drive')
        pois = ox.features.features_from_polygon(place, tags=tags)


    # Converter para MultiGraph não-direcionado
    G_undirected = to_undirected_multigraph(G)


    # Extrair pontos representativos (centroides se for polígono)
    poi_points = []
    for idx, row in pois.iterrows():
        if row.geometry.geom_type == 'Point':
            poi_points.append((row.geometry.y, row.geometry.x))
        elif row.geometry.geom_type in ['Polygon', 'MultiPolygon']: # Handle Polygons and MultiPolygons
             # Use the centroid of the polygon as the representative point
             centroid = row.geometry.centroid
             poi_points.append((centroid.y, centroid.x))
        else: # Handle other geometry types if necessary, or skip
             print(f"Geometria não suportada para o POI {idx}: {row.geometry.geom_type}. Pulando.")


    if not poi_points:
        print(f"Nenhum POI encontrado para {place} com as tags {tags}. Pulando.")
        return G, G_undirected, [], []

    # Encontrar os nós mais próximos no grafo para essas coordenadas
    longitudes = [hp[1] for hp in poi_points]
    latitudes = [hp[0] for hp in poi_points]
    # Use o grafo direcionado para encontrar os nós mais próximos
    poi_nodes = ox.distance.nearest_nodes(G, X=longitudes, Y=latitudes)
    poi_nodes = list(set(poi_nodes)) # Remover duplicados

    if len(poi_nodes) < 2:
        print(f"POIs insuficientes ({len(poi_nodes)}) encontrados para {place} para construir um MST. Pulando.")
        return G, G_undirected, poi_points, []

    print(f"Encontrados {len(poi_points)} POIs e {len(poi_nodes)} nós mais próximos únicos para {place}.")
    return G, G_undirected, poi_points, poi_nodes
```

<p align = "justify">A função `build_poi_graph(G, poi_nodes)` constrói um novo grafo em que cada vértice representa um nó da rede de ruas mais próximo a um ponto de interesse (POI). Inicialmente, ela cria um grafo vazio e percorre todos os pares possíveis de nós únicos fornecidos. Para cada par, calcula o caminho mais curto na rede original utilizando o algoritmo A*, cuja heurística é a distância euclidiana entre as coordenadas geográficas dos nós. O peso considerado é o comprimento das arestas, e após encontrar o caminho, a função calcula sua extensão total e adiciona uma aresta ao novo grafo com esse valor como peso. A rota real também é armazenada como atributo da aresta para uso posterior. Caso não seja possível encontrar uma rota entre dois nós, por exemplo, em partes desconectadas da rede, a função imprime uma mensagem e ignora o par. Ao final, remove quaisquer arestas com peso zero ou inválido, garantindo a integridade do grafo de interesse.
</p>

```
def build_poi_graph(G, poi_nodes):
    G_interest = nx.Graph()
    for i in range(len(poi_nodes)):
        for j in range(i + 1, len(poi_nodes)):
            u = poi_nodes[i]
            v = poi_nodes[j]
            # Usando A* com a heurística euclidiana e peso 'length'
            try:
                route = nx.astar_path(
                    G,
                    u,
                    v,
                    heuristic=lambda n1, n2: ox.distance.euclidean(
                        G.nodes[n1]['y'], G.nodes[n1]['x'],
                        G.nodes[n2]['y'], G.nodes[n2]['x']
                    ),
                    weight='length'
                )
                route_length = nx.astar_path_length(
                    G,
                    u,
                    v,
                    heuristic=lambda n1, n2: ox.distance.euclidean(
                        G.nodes[n1]['y'], G.nodes[n1]['x'],
                        G.nodes[n2]['y'], G.nodes[n2]['x']
                    ),
                    weight='length'
                )
                G_interest.add_edge(u, v, weight=route_length, route=route)
            except nx.NetworkXNoPath:
                print(f"Nenhuma rota encontrada entre {u} e {v}. Ignorando este par.")
                continue

    # Remover arestas com peso 0 ou infinito
    edges_to_remove = [(u, v) for u, v, d in G_interest.edges(data=True) if d['weight'] == 0 or not isinstance(d['weight'], (int, float))]
    G_interest.remove_edges_from(edges_to_remove)

    if G_interest.number_of_nodes() < 2 or G_interest.number_of_edges() == 0:
         print("O grafo de POIs não possui nós ou arestas suficientes para calcular o MST.")

    return G_interest
```

A função `calculate_mst_lengths(G, G_interest)` calcula os comprimentos associados à Árvore Geradora Mínima (MST) no grafo construído a partir dos pontos de interesse (POIs). Primeiramente, ela verifica se o grafo de interesse (`G_interest`) é válido para esse cálculo — ou seja, se possui pelo menos dois nós e ao menos uma aresta. Caso contrário, retorna comprimentos iguais a zero.

Se o grafo for válido, a função aplica o algoritmo de Kruskal (`nx.minimum_spanning_edges`) para gerar a MST, que é um subgrafo que conecta todos os vértices com o menor peso total possível. Os pesos utilizados são as distâncias A* previamente calculadas entre os pares de POIs. A soma desses pesos resulta no `total_mst_length`, que representa o comprimento mínimo teórico necessário para conectar todos os POIs com base nas rotas mais curtas.

Em seguida, a função identifica todas as arestas da rede de ruas original (`G`) que compõem as rotas A* correspondentes às arestas da MST. Arestas duplicadas são removidas, já que diferentes rotas podem compartilhar trechos da malha viária. O comprimento total dessas arestas únicas é calculado e armazenado como `total_real_mst_length`, que representa o comprimento real das vias urbanas utilizadas para conectar os POIs conforme a MST.

Por fim, a função retorna os dois valores: `total_mst_length` e `total_real_mst_length`.

```
def calculate_mst_lengths(G, G_interest):
    if G_interest.number_of_nodes() < 2 or G_interest.number_of_edges() == 0:
         print("Não é possível calcular os comprimentos da MST: o grafo de POIs não é válido.")
         return 0, 0

    # Calcular a MST no grafo de POIs
    mst_edges = list(nx.minimum_spanning_edges(G_interest, data=True, algorithm='kruskal'))
    total_mst_length = sum([d['weight'] for (u, v, d) in mst_edges])

    # Coletar todas as arestas das rotas que compõem a MST
    all_mst_route_edges = []
    for (u, v, d) in mst_edges:
        if 'route' in d:
            route = d['route']
            for i in range(len(route) - 1):
                node1 = route[i]
                node2 = route[i+1]
                # Precisa considerar arestas paralelas potenciais no MultiDiGraph G
                edges = G.get_edge_data(node1, node2)
                if edges:
                    for key in edges:
                         all_mst_route_edges.append((node1, node2, key))

    # Remover arestas duplicadas
    unique_mst_route_edges = list(set(all_mst_route_edges))

    # Calcular o comprimento total dessas arestas únicas no grafo G
    total_real_mst_length = 0
    for u, v, key in unique_mst_route_edges:
        if 'length' in G.edges[u, v, key]:
            total_real_mst_length += G.edges[u, v, key]['length']
        else:
            print(f"A aresta ({u}, {v}, {key}) não possui um atributo 'length'.")

    return total_mst_length, total_real_mst_length
```

<p align = "justify">Após o desenvolvimento das funções, foi estruturada uma lista com os locais a serem analisados. Essa seleção contempla capitais, cidades históricas, insulares e regiões urbanas com alta complexidade, permitindo uma abordagem comparativa robusta entre diferentes contextos geográficos e padrões de urbanização. </p>

```
# Lista de 8 cidades brasileiras a serem analisadas
cities = [
    "Natal, Rio Grande do Norte, Brazil",
    "Palmas, Tocantins, Brazil",
    # Polígono aproximado para a Zona Norte de São Paulo
    {"name": "São Paulo Zona Norte, São Paulo, Brasil", "polygon": shapely.geometry.box(-46.75, -23.4, -46.5, -23.2)}, # Zona Norte
    "Parintins, Amazonas, Brazil",
    "Florianópolis, Santa Catarina, Brazil",
    "Ouro Preto, Minas Gerais, Brazil",
    "Juiz de Fora, Minas Gerais, Brazil",
    "Santos, São Paulo, Brazil"
]

# Tag do tipo de POI para "Bancos"
tags = {'amenity': 'bank'}

print("Cidades/Regiões a serem analisadas:", cities)
print("Tags dos POIs:", tags)
```

### 2.3 Papeline automatizado para coleta de informações

<p align = "justify">O algoritmo de coleta desenvolvido neste trabalho automatiza o processo de extração e análise de dados espaciais para oito cidades brasileiras. Ele integra funções que acessam a rede viária via OSMnx, identificam pontos de interesse (POIs) — neste caso, agências bancárias — e constroem grafos de conectividade entre esses pontos. A partir disso, calcula rotas mínimas com o algoritmo A* e gera a Árvore Geradora Mínima (MST), permitindo avaliar a infraestrutura viária necessária para conectar os serviços essenciais em diferentes contextos urbanos.</p>

```# Define as tags pois são necessárias para get_graph_pois_nodes
tags = {'amenity': 'bank'}

# Iterar através de cada cidade nos resultados da análise
for result in analysis_results:
    city_name = result['city']
    print(f" >> Visualizando MST para {city_name}...")

    # Encontrar a geometria do local original (string ou polígono) da lista 'cities'
    # Acessar a variável 'cities' diretamente, pois ela está definida em uma célula anterior
    place_geom = None
    # Iterar através da lista original de cidades para encontrar o place_geom correto
    for city_info in cities:
        if isinstance(city_info, str) and city_info == city_name:
            place_geom = city_info
            break
        elif isinstance(city_info, dict) and city_info.get("name") == city_name:
            place_geom = city_info.get("polygon")
            break

    if place_geom is None:
        print(f"Erro: Geometria do local não encontrada para {city_name}. Pulando visualização.")
        continue

    # Re-buscar dados do grafo e POI
    # Usar o grafo direcionado original G para plotar rotas
    G, G_undirected, poi_points, poi_nodes = get_graph_pois_nodes(place_geom, tags)

    if not poi_nodes or len(poi_nodes) < 2:
        print(f"POIs insuficientes para visualizar MST em {city_name}. Pulando.")
        continue

    # Reconstruir o Grafo de POI e calcular as arestas da MST
    try:
        G_interest = build_poi_graph(G, poi_nodes)
        if G_interest.number_of_nodes() < 2 or G_interest.number_of_edges() == 0:
             print(f"Grafo de POIs inválido para visualizar MST em {city_name}. Pulando.")
             continue

        mst_edges = list(nx.minimum_spanning_edges(G_interest, data=True, algorithm='kruskal'))

        # Extrair rotas da MST (lista de IDs de nós)
        mst_routes_list = [d['route'] for (u, v, d) in mst_edges if 'route' in d]

        # Preparar a figura e os eixos
        fig, ax = plt.subplots(figsize=(10, 10))

        # Desenhar o grafo viário (em cinza, sem nós)
        # Use show=False e close=False para desenhar em eixos existentes
        ox.plot_graph(G, ax=ax, node_size=0, edge_color='gray', bgcolor='white', show=False, close=False)

        # Obter coordenadas dos nós dos POIs no grafo original G
        # Certifique-se de que os nós em poi_nodes existem no grafo G
        poi_nodes_in_G = [n for n in poi_nodes if n in G.nodes]

        # Converter e plotar os nós POI como esferas azuis
        # Use as coordenadas do grafo G
        poi_x = [G.nodes[n].get('x') for n in poi_nodes_in_G]
        poi_y = [G.nodes[n].get('y') for n in poi_nodes_in_G]
        ax.scatter(poi_x, poi_y, c='red', s=60, zorder=5, label=f'POIs (N={len(poi_nodes_in_G)})') # Cor azul e label com N de nós únicos

        # Plotar rotas da MST em azul
        for route in mst_routes_list:
            # Obter coordenadas dos nós da rota no grafo original G
            route_x = [G.nodes[n].get('x') for n in route if n in G.nodes]
            route_y = [G.nodes[n].get('y') for n in route if n in G.nodes]
            # Plotar a rota individualmente em azul
            ax.plot(route_x, route_y, linewidth=2, color='blue', zorder=4, alpha=0.7)

        # Configurar título e legenda
        ax.set_title(f"Rotas da MST e POIs para {city_name}", fontsize=16)
        ax.legend()

        # Remover eixos
        ax.set_axis_off()

        plt.tight_layout()
        plt.show()

    except Exception as e:
        print(f"Ocorreu um erro durante a visualização para {city_name}: {e}")
        continue

print("\nVisualização da MST para todas as cidades concluída.")
```


## 3. Resultados











