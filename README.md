---
jupyter:
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
  language_info:
    codemirror_mode:
      name: ipython
      version: 3
    file_extension: .py
    mimetype: text/x-python
    name: python
    nbconvert_exporter: python
    pygments_lexer: ipython3
    version: 3.10.12
  nbformat: 4
  nbformat_minor: 5
---

::: {#f842ba93-f137-4b90-a236-bc325122d610 .cell .markdown}
# Introdução ao Google Earth Engine
:::

::: {#f2638f59-93be-4de7-9178-da1389f37edd .cell .markdown}
## Sensoriamento remoto via satélite
:::

::: {#43fe9215-5540-48f7-b2b5-ef676d7820a1 .cell .markdown}
Atualmente, há cerca de 5.000 satélites ativos orbitando a Terra. Este
número inclui satélites de comunicação, meteorológicos, de observação da
Terra, científicos, militares, entre outros.

O número de sensores por satélite varia conforme o propósito do
satélite. Satélites de observação da Terra, como os da série Landsat,
Sentinel e outros, geralmente possuem entre 1 a 10 sensores, dependendo
da sua missão específica.

O **Landsat 8** possui dois sensores principais: **OLI** (Operational
Land Imager) e **TIRS** (Thermal Infrared Sensor). Enquanto cada
satélite da série **Sentinel-2** possui um sensor **MSI** (MultiSpectral
Instrument).

A quantidade de imagens geradas por mês também varia conforme a
capacidade dos satélites e a demanda por dados. Para satélites de
observação da Terra, pode-se estimar que cada satélite pode gerar entre
algumas centenas a milhares de imagens por mês.

Cada satélite **Sentinel-2** pode gerar cerca de 100 terabytes de dados
por mês, com imagens cobrindo grandes áreas da superfície terrestre. O
programa **Landsat** produz centenas de imagens diariamente, acumulando
milhares de imagens por mês.

O **Sentinel-2** é uma parte crucial do projeto **Copernicus**, uma
iniciativa conjunta da Comissão Europeia e da Agência Espacial Europeia
(ESA) para observar a Terra. **Sentinel-2** consiste em um conjunto de
dois satélites: **Sentinel-2A** e **Sentinel-2B**, lançados em 2015 e
2017, respectivamente.

**Principais Características**

-   **Objetivo**: Fornecer imagens multiespectrais de alta resolução
    para monitoramento ambiental, agricultura, florestas, gestão de
    desastres, cartografia e segurança.
-   **Sensores**: Cada satélite possui um sensor MSI (MultiSpectral
    Instrument) que captura imagens em 13 bandas espectrais, variando do
    visível ao infravermelho próximo e de ondas curtas.
-   **Resolução Espacial**: Oferece resoluções de 10, 20 e 60 metros,
    dependendo da banda espectral.
-   **Cobertura**: Tem uma ampla faixa de varredura de 290 km,
    permitindo uma cobertura rápida e frequente da superfície terrestre,
    revisitando a mesma área a cada 5 dias.
-   **Dados**: Os dados são gratuitos e abertos ao público, promovendo a
    sua ampla utilização em diversas aplicações científicas, comerciais
    e ambientais.

**Aplicações**

-   **Agricultura**: Monitoramento do crescimento das culturas, saúde
    das plantas e planejamento agrícola.
-   **Florestas**: Mapeamento e monitoramento das florestas, detecção de
    desmatamento e análise de mudanças na cobertura florestal.
-   **Água e Costas**: Monitoramento de corpos d\'água, qualidade da
    água e áreas costeiras.
-   **Desastres Naturais**: Apoio na gestão e resposta a desastres
    naturais como inundações, incêndios florestais e deslizamentos de
    terra.
-   **Uso do Solo e Urbanização**: Análise e mapeamento do uso do solo,
    expansão urbana e planejamento territorial.

O **Sentinel-2**, com seus dados de alta qualidade e acessibilidade, é
uma ferramenta essencial para a gestão sustentável dos recursos naturais
e para enfrentar desafios ambientais globais.
:::

::: {#315077b4-28c5-42b2-9d34-1113768dd666 .cell .markdown}
## Google Earth Engine
:::

::: {#6a567de8-f678-4e7f-9e0f-910b5694245c .cell .markdown}
**Google Earth Engine** é um catálogo que contém mais de 80 *petabytes*
de dados geospaciais, disponíveis para análise e visualização, desde que
o usuário cadastre uma **api key**.

Conta também com um editor de código
(<https://code.earthengine.google.com/>), útil para extrair as
coordenadas da área de interesse, caso você prefira outro ambiente de
desenvolvimento.
:::

::: {#f6f773fb-6a19-4e7b-a181-751d9fe0ee5c .cell .markdown}
## Procedimentos básicos
:::

::: {#d6c889d0-b35e-4eac-915e-638b1e20c625 .cell .markdown}
### Importamos as bibliotecas
:::

::: {#88137d5a-7f55-49d6-b61a-5c9dd6067db8 .cell .code execution_count="1"}
``` python
import ee
import geemap
import rasterio
from rasterio.plot import show
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
```
:::

::: {#fce744bb-597e-4aa0-9258-58abab4f9d8f .cell .markdown}
### Inicializamos a API
:::

::: {#90303401-8359-4c8e-a566-769185415112 .cell .code execution_count="2"}
``` python
# Processo de autenticação
ee.Authenticate()

# Inicializamos a biblioteca
ee.Initialize()
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::
:::

::: {#1ba6ac6a-0002-48a4-9999-7a5ce70a46ba .cell .markdown}
### Intervalo temporal

Definimos o período para captura das imagens:
:::

::: {#f7a3afdc-c959-462a-bc55-da3f30959276 .cell .code execution_count="3"}
``` python
data_inicial = '2024-04-01'
data_final = '2024-07-31'
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::
:::

::: {#1d11e81c-daea-407b-9259-d62ae2359f78 .cell .markdown}
### Área de interesse

Definimos as coordenadas da área de interesse:
:::

::: {#3b09a50a-78c9-48e7-87a3-0177e014b086 .cell .code execution_count="4"}
``` python
polygon_coords = [
    [-43.41287287680422, -21.756108688468274],
    [-43.396307553928246, -21.756108688468274],
    [-43.396307553928246, -21.750887131860495],
    [-43.41287287680422, -21.750887131860495],
    [-43.41287287680422, -21.756108688468274]
]

aoi = ee.Geometry.Polygon(polygon_coords, None, False)
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::
:::

::: {#2e2ef316-6ac3-4fbc-83f8-b844b6433dcf .cell .markdown}
### Definimos a coleção de imagens
:::

::: {#9e0fe117-a1ce-4f6e-9f58-33690e6c8e9d .cell .markdown}
Aplicamos os seguintes filtros:

-   área de interesse;
-   intervalo temporal;
-   seleção de bandas
:::

::: {#619325af-2cdb-4e69-98db-baa391028a7d .cell .code execution_count="5"}
``` python
sel_bandas = ('B1', 'B2', 'B3', 'B4', 'B5')

db = ee.ImageCollection('COPERNICUS/S2_SR_HARMONIZED') \
                .filterBounds(aoi) \
                .filterDate(ee.Date(data_inicial), ee.Date(data_final)) \
                .select(sel_bandas)
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::
:::

::: {#2f57fbb5-0f57-490b-a30e-da2f2c219091 .cell .markdown}
### Extraímos a imagem com menor cobertura de nuvens
:::

::: {#615f8a71-7783-4e56-8e10-52008acd7fcd .cell .code execution_count="6"}
``` python
img = ee.Image(db.sort('CLOUDY_PIXEL_PERCENTAGE').first())
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::
:::

::: {#6ffb42ee-da71-4e54-84c8-9de35fcc9a1b .cell .markdown}
### Salvamos a imagem no formato tif
:::

::: {#14e316c5-c9f5-494e-99a6-59cb01d70458 .cell .code execution_count="7"}
``` python
filename = 'teste.tif'
geemap.ee_export_image(img, filename=filename, scale=10, region=aoi, file_per_band=False)
    
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::

::: {.output .stream .stdout}
    Generating URL ...
    Downloading data from https://earthengine.googleapis.com/v1/projects/earthengine-legacy/thumbnails/19173b9b5d62449fae0e3e3917e4cb32-a66a6b3909912300c02b12e1d56ca593:getPixels
    Please wait ...
    Data downloaded to /home/gf/jupyterlab_projects/Sat_Images/teste.tif
:::
:::

::: {#eced7a80-879a-4c99-823e-265175dedf80 .cell .markdown}
### Abrimos o arquivo TIFF
:::

::: {#cde6d16d-57dc-49f2-a035-59845b0e7076 .cell .code execution_count="8"}
``` python
# Abra o arquivo TIFF
with rasterio.open('teste.tif') as dataset:
    multibanda = dataset.read()
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::
:::

::: {#e00b17c7-e22f-44b1-85ab-d910b1adbc1e .cell .markdown}
### Selecionamos uma banda espectral
:::

::: {#173a7dd0-6f82-4d8c-8cbe-d265d2f00ed0 .cell .code execution_count="9"}
``` python
with rasterio.open('teste.tif') as dataset:
    band3 = dataset.read(3)
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::
:::

::: {#6a113f25-2f78-4f95-9434-d65905e15aed .cell .markdown}
### Visualizamos a imagem com Matplotlib
:::

::: {#c5dd90d2-76d1-41dd-ba55-50b2d735c2c4 .cell .code execution_count="10"}
``` python

plt.imshow(band3, cmap='pink')
plt.show()
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::

::: {.output .display_data}
![](vertopal_7012e0f2f0664d499210b7fa1cd6df73/6c4a42d0fa5e5c179c132a00eadcb5c46ad690b2.png)
:::
:::

::: {#ff7bc749-78a9-447a-b0ba-5cff89d80ae6 .cell .markdown}
### Alteramos o formato da imagem e o mapa de cores
:::

::: {#886bd9bf-f166-4699-9b4a-64b241467808 .cell .code execution_count="11"}
``` python
# Abrir a imagem GeoTIFF
with rasterio.open('teste.tif') as src:
    imagem = src.read(3)
    metados = src.meta

# Plotar a imagem e o polígono usando matplotlib e rasterio
fig, ax = plt.subplots(figsize=(10, 10))
show(imagem, transform=src.transform, cmap='RdYlGn', ax=ax, interpolation='none')

# Configurar o título e exibir o plot
ax.set_title('Imagem com polígono de interesse')
plt.xlabel('Longitude')
plt.ylabel('Latitude')
plt.show()
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::

::: {.output .display_data}
![](vertopal_7012e0f2f0664d499210b7fa1cd6df73/3bb1bcadfac962af7179fd2ca01c8cce6817fb5b.png)
:::
:::

::: {#3fb195a7-a2e4-4d73-89cc-507fdd754d06 .cell .markdown}
### Visualizamos diferentes bandas com rasterio.plot {#visualizamos-diferentes-bandas-com-rasterioplot}
:::

::: {#1b6f606e-6316-4b62-adfd-d7f8e2800b56 .cell .code execution_count="12"}
``` python
# Abrir o arquivo raster
with rasterio.open('teste.tif', 'r') as raster:
    # Criar a figura com 2 colunas e 2 linhas
    fig, axs = plt.subplots(ncols=2, nrows=2, figsize=(12, 10))

    # Plotar as bandas
    show((raster, 2), cmap='Greens', ax=axs[0, 0])
    show((raster, 3), cmap='Reds', ax=axs[0, 1])
    show((raster, 5), cmap='magma', ax=axs[1, 0])
    show((raster, 4), cmap='inferno', ax=axs[1, 1])

    # Adicionar títulos aos gráficos
    axs[0, 0].set_title("Green")
    axs[0, 1].set_title("Red")
    axs[1, 0].set_title("NIR")
    axs[1, 1].set_title("VNIR")
    fig.tight_layout()
       
    plt.show()
```

::: {.output .display_data}
```{=html}

            <style>
                .geemap-dark {
                    --jp-widgets-color: white;
                    --jp-widgets-label-color: white;
                    --jp-ui-font-color1: white;
                    --jp-layout-color2: #454545;
                    background-color: #383838;
                }

                .geemap-dark .jupyter-button {
                    --jp-layout-color3: #383838;
                }

                .geemap-colab {
                    background-color: var(--colab-primary-surface-color, white);
                }

                .geemap-colab .jupyter-button {
                    --jp-layout-color3: var(--colab-primary-surface-color, white);
                }
            </style>
            
```
:::

::: {.output .display_data}
![](vertopal_7012e0f2f0664d499210b7fa1cd6df73/db85dbedc8c7ab9efcadc1ae94b5185820d5c9c7.png)
:::
:::
