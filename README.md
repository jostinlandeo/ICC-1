# ICC-1 
#se recreará el sorteo de los paises para el mundial y sus respectivos puntajes de acuerdo a una tabla de probabilidades
#El código necesita de la tabla de probalidades a modo de matriz, y se realizó el mismo trabajo para la entrega en el laboratorio de ICC
import random

#Lista de paises en orden alfabetico
archivos_paises = 'paises.txt'
lista_paises = open(archivos_paises, 'r')
paises = lista_paises.readline().split(",")
paises = sorted(paises)

#matriz de paises extraidas de excel
path = 'probabilidadesproyecto.csv'
matrix_file = open(path, 'r')
matrix = []
for i in range(33):
    matrix.append(matrix_file.readline().split(","))
for i in range(len(matrix)):
    matrix[i][32] = matrix[i][32][:-1]

#creamos las listas vacias para los grupos
grupoA = []
grupoB = []
grupoC = []
grupoD = []
grupoE = []
grupoF = []
grupoG = []
grupoH = []

#nombre de cada grupo
nombre_grupos = ["GRUPO A: ", "GRUPO B: ", "GRUPO C: ", "GRUPO D: ", "GRUPO E: ", "GRUPO F: ", "GRUPO G: ", "GRUPO H: "]
#nombre de cada partido
partidos = ["PARTIDO 1: ", "PARTIDO 2: ", "PARTIDO 3: ", "PARTIDO 4: ", "PARTIDO 5: ", "PARTIDO 6: ", "PARTIDO 7: ", "PARTIDO 8: "]

#La funcion se encarga de sacar los bombos de los archivos
#de texto y los convierte a lista
def invocar_bombo(numero_bombo):
    #leemos los archivos de la carpeta
    path = 'bombo' + str(numero_bombo) + '.txt'
    bombo_file = open(path, 'r')
    bombo = bombo_file.readline().split(",")
    return bombo

#La funcion se encarga de crear los grupos a partir de los
#bombos y los grupos metidos ambos en una lista
def crear_grupos(lista_bombos, lista_grupos):
    for i in lista_bombos:
        for j in lista_grupos:
            posicion = random.randint(0, len(i)-1)
            j.append(i[posicion])
            i.remove(i[posicion])
    return grupos

#La funcion se encarga de calcular el puntaje de cada partido
def puntaje_partido(probabilidad):
    puntaje = 0
    if float(probabilidad) > 0.5:
        puntaje = 3
    elif float(probabilidad) < 0.5:
        puntaje = 0
    elif float(probabilidad) == 0.5:
        puntaje = 1
    return puntaje

#La funcion se encarga de calcular los puntajes de cada grupo
def puntajes_grupos(paises, grupos):
    puntaje_Grupo_A = []
    puntaje_Grupo_B = []
    puntaje_Grupo_C = []
    puntaje_Grupo_D = []
    puntaje_Grupo_E = []
    puntaje_Grupo_F = []
    puntaje_Grupo_G = []
    puntaje_Grupo_H = []

    puntajes = [puntaje_Grupo_A, puntaje_Grupo_B, puntaje_Grupo_C, puntaje_Grupo_D, puntaje_Grupo_E, puntaje_Grupo_F, puntaje_Grupo_G, puntaje_Grupo_H]

    contador = 0
    for k in grupos:
        for i in k:
            puntaje = 0
            for j in k:
                posicion_pais = paises.index(i)+1
                posicion_pais_contrincante = paises.index(j)+1
                probabilidad = matrix[posicion_pais][posicion_pais_contrincante]
                if probabilidad == "":
                    if i == j:
                        puntaje += 0
                    elif i != j:
                        probabilidad = 1 - float(matrix[posicion_pais_contrincante][posicion_pais])
                        puntaje += puntaje_partido(probabilidad)
                elif float(probabilidad) >= 0.00:
                    puntaje += puntaje_partido(probabilidad)
            puntajes[contador].append(puntaje)
        contador += 1
    for i in range(7, 0, -1):
        if puntajes[i] == []:
            puntajes.pop()
    return puntajes

#La funcion se encarga de ordenar los grupos por puntaje
def orden_grupo_puntaje(grupos, puntajes):
    orden_grupos = []
    for i in range(len(grupos)):
        Z = [x for y, x in sorted(zip(puntajes[i], grupos[i]))]
        Z = Z[::-1]
        orden_grupos.append(Z)
    return orden_grupos

#La funcion creara una lista con los dos ganadores por cada grupo
def ganador_grupos(grupo_ordenado):
    ganador_grupoA = []
    ganador_grupoB = []
    ganador_grupoC = []
    ganador_grupoD = []
    ganador_grupoE = []
    ganador_grupoF = []
    ganador_grupoG = []
    ganador_grupoH = []

    ganador_grupos = [ganador_grupoA, ganador_grupoB, ganador_grupoC, ganador_grupoD, ganador_grupoE, ganador_grupoF, ganador_grupoG, ganador_grupoH]

    contador = 0
    for i in ganador_grupos:

        for j in range(2):
            i.append(grupo_ordenado[contador][j])
        contador +=1
    return ganador_grupos

#Aqui se guardaron las listas bombos
bombo1 = invocar_bombo(1)
bombo2 = invocar_bombo(2)
bombo3 = invocar_bombo(3)
bombo4 = invocar_bombo(4)

print("BOMBOS")
print("BOMBO 1:", ", ".join(bombo1))
print("BOMBO 2:", ", ".join(bombo2))
print("BOMBO 3:", ", ".join(bombo3))
print("BOMBO 4:", ", ".join(bombo4))
print(" ")

#creamos las listas de bombos y grupos
bombos = [bombo1[:], bombo2[:], bombo3[:], bombo4[:]]
grupos = [grupoA, grupoB, grupoC, grupoD, grupoE, grupoF, grupoG, grupoH]

#aplicamos la funcion para crear los grupos
crear_grupos(bombos,grupos)

#Imprimimos cada grupo
print("GRUPOS")
for i in range(8):
    print(nombre_grupos[i], ", ".join(grupos[i]), sep="")
print(" ")

#PUNTAJES de cada equipo
puntajes_por_grupo = puntajes_grupos(paises, grupos)

#Se crea una nueva lista con los grupos ordenados por puntaje
grupos_ordenados = orden_grupo_puntaje(grupos, puntajes_por_grupo)

#Se crea una lista con los puntajes ordenados
puntajes_ordenados = []
for i in puntajes_por_grupo:
    puntajes_ordenados.append(sorted(i)[::-1])

#Se imprime los grupos en orden con su respeotivo puntaje
print("POSICIONES")
for i in range(8):
    print(nombre_grupos[i], end="")
    for j in range(4):
        if j != 3:
            print(j+1,":", grupos_ordenados[i][j], " (", puntajes_ordenados[i][j], " pts)", sep="", end=", ")
        else:
            print(j+1,":", grupos_ordenados[i][j], " (", puntajes_ordenados[i][j], " pts)", sep="", end=" ")
    print(" ")
print(" ")

#Se crea una lista con los ganadores de cada grupo
ganadores = ganador_grupos(grupos_ordenados)
