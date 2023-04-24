
![Ironhack logo](https://i.imgur.com/1QgrNNw.png)
# Proyecto_Sharks

## Introducción

El siguiente proyecto consiste en la limpieza de datos del archivo attack.csv, una base de datos que contiene una recopilación de ataques de tiburones en un periodo de más de 2000 años. El archivo contiene información muy útil como el nombres de las personas atacadas, su edad, actividad que estaban realizando, fecha/hora/lugar del incidente e incluso la especie del tiburón.

La información del dataset no aparece de forma clara, hay muchos valores incompletos, vacíos, mal redactados y una gran cantidad de filas. Aún así, hay información suficiente para realizar un análisis y obtener información valiosa.

## Objetivos

1. Realizar un análisis inicial
2. Eliminar los valores nulos
3. Reemplazar los valores inconsistentes por valores que nos puedan ser útiles a la hora de realizar un análisis.
4. Arreglar datos
5. Deben quedar al menos 6000 filas
6. No se pueden eliminar las columnas
7. Establecer un objetivo del análisis (Hipótesis) y desarrollarlo

## Data cleaning

El primer paso a seguir es eliminar los valores nulos. Para ello, visualizamos el porcentaje de nulos por columna. Sorprendentemente comprobamos que en casi todas las columnas hay más de un 75% de valores nulos. Para tratar estos nulos, resulta muy efectivo realizar un gráfico de nulos, para que podamos observar la distribución de nulos. 

Observamos que en todas las columnas ha debido haber un error de encodificación y desde la columna 63003 hasta la 25000 todos los valores son nulos, porque eliminamos estas filas.

Comprobamos también si existe alguna columna duplicada y descubrimos que hay 3 columnas duplicadas. Procedemos a eliminarlas.

Para realizar una limpieza de datos más exhaustiva, vamos a ir analizando el contenido de las columnas una por una, investigando el resto de valores nulos o inconsistentes.

-Columna Fatal(Y/N): para rellenar los valores nulos, comprobamos si la palabra "FATAL" se encuentra en la columna "Injury" y rellenamos con "Y" si está o con "N" si no está.

            data['Fatal(Y/N)'] = data.apply(lambda x: "Y" if "FATAL" in x['Injury'] else "N" if pd.isnull(x['Fatal(Y/N)']) else x['Fatal(Y/N)'], axis=1)

-Columna Área y Location: rellenamos los valores de la columna "Área" con los valores de la columna "Country" y los valores de la columna "Location" con los valores de la columna "Área".

-Columna Sex: reemplazamos los valores que no son ni "M" ni "F" con "unknown", analizamos los valores residuales y los clasificamos como "unknown".

-Columna Activity: hay una gran catidad de categorías, por lo que elegimos sintetizarlas en categorías más generales:

    reemplazos = {'surf': 'Surfing', 'swim': 'Swimming', 'fishing': 'Fishing', 'spearfish': 'Spearfishing', 'bath': 'Bathing', 'wade': 'Wading', 'dive': 'Diving', 'stand': 'Standing', 'snorkel': 'Snorkeling'}

            for i in range(len(data['Activity'])):
                if isinstance(data['Activity'].iloc[i], str):
                    for palabra in reemplazos:
                        if palabra in data['Activity'].iloc[i]:
                            data['Activity'].iloc[i] = reemplazos[palabra]
                            break

            data['Activity'] = data['Activity'].replace(reemplazos.values(), reemplazos.keys())

-Columna Date: rellenamos los valores nulos con el valor más reciente y trasnformamos la columna un formato general.

            data["CaseNumber"] = pd.to_datetime(data["CaseNumber"], errors='coerce')
            data["CaseNumber"].fillna(method="ffill", inplace=True)
            data["Date"] = data["CaseNumber"].apply(lambda x: x.strftime("%Y-%m-%d %H:%M:%S"))

-Columna Type: trasnformamos los valores de la columna a uns categorías más entendibles: Attacks, Accidents, Unknown

            for i in range(len(data['Type'])):
                if data['Type'][i] in ['Questionable', 'Boatomg']:
                    pass
                elif data['Type'][i] in ['Unprovoked', 'Provoked']:
                    data['Type'][i] = 'Attacks'
                elif data['Type'][i] in ['Sea Disaster', 'Boating']:
                    data['Type'][i] = 'Accidents'
                else:
                    data['Type'][i] = 'Unknown'

-Resto de columnas: rellenamos los nulos con "unknown"




