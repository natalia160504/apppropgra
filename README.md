import streamlit as st
import pandas as pd
import altair as alt

st.set_page_config(
    page_title="El agua en México: Disponibilidad y Desperdicio 💧",
    layout="centered"
)

st.markdown("""
<style>
.main {
    background-color: #F4F8FB;
}
h1 {
    color: #1B4965;
    text-align: center;
}
h3 {
    color: #62B6CB;
}
</style>
""", unsafe_allow_html=True)

st.title("💧 El agua en México: disponibilidad, calidad y desperdicio")

st.write(
    "Aplicación interactiva desarrollada para analizar la situación "
    "hídrica por estados, comprender los indicadores oficiales y "
    "concientizar sobre el consumo en actividades cotidianas."
)

# =====================================================
# 1. DATOS DE LOS ESTADOS
# =====================================================

estados_mexico = [
    "Aguascalientes", "Baja California", "Baja California Sur",
    "Campeche", "Chiapas", "Chihuahua", "Ciudad de México",
    "Coahuila", "Colima", "Durango", "Estado de México",
    "Guanajuato", "Guerrero", "Hidalgo", "Jalisco",
    "Michoacán", "Morelos", "Nayarit", "Nuevo León",
    "Oaxaca", "Puebla", "Querétaro", "Quintana Roo",
    "San Luis Potosí", "Sinaloa", "Sonora", "Tabasco",
    "Tamaulipas", "Tlaxcala", "Veracruz", "Yucatán",
    "Zacatecas"
]

estados_mexico = sorted(estados_mexico)

periodos = [
    "2006-2010",
    "2011-2015",
    "2016-2020",
    "2021-2025",
    "2026-2030",
    "Dato más reciente disponible"
]

# =====================================================
# 2. DISPONIBILIDAD DE AGUA
# =====================================================

datos_disponibilidad = {
    "Aguascalientes": [420, 390, 360, 340],
    "Baja California": [980, 920, 870, 810],
    "Baja California Sur": [780, 730, 690, 650],
    "Campeche": [18500, 18000, 17500, 16900],
    "Chiapas": [24000, 23200, 22500, 21800],
    "Chihuahua": [1850, 1720, 1610, 1540],
    "Ciudad de México": [160, 150, 140, 130],
    "Coahuila": [1450, 1380, 1310, 1250],
    "Colima": [3200, 3050, 2900, 2780],
    "Durango": [3100, 2950, 2800, 2680],
    "Estado de México": [260, 240, 220, 210],
    "Guanajuato": [680, 630, 590, 550],
    "Guerrero": [5800, 5500, 5200, 4950],
    "Hidalgo": [1950, 1850, 1750, 1680],
    "Jalisco": [1550, 1470, 1390, 1320],
    "Michoacán": [2900, 2750, 2600, 2480],
    "Morelos": [1150, 1080, 1020, 970],
    "Nayarit": [7800, 7400, 7000, 6650],
    "Nuevo León": [1300, 1200, 1100, 1020],
    "Oaxaca": [8500, 8100, 7700, 7300],
    "Puebla": [950, 890, 840, 790],
    "Querétaro": [820, 770, 720, 680],
    "Quintana Roo": [12500, 11800, 11100, 10500],
    "San Luis Potosí": [1650, 1560, 1480, 1400],
    "Sinaloa": [5200, 4900, 4600, 4350],
    "Sonora": [2800, 2650, 2500, 2380],
    "Tabasco": [22000, 21500, 20800, 19900],
    "Tamaulipas": [2100, 1980, 1870, 1780],
    "Tlaxcala": [510, 480, 450, 420],
    "Veracruz": [11500, 10900, 10300, 9800],
    "Yucatán": [9200, 8700, 8200, 7800],
    "Zacatecas": [1250, 1180, 1110, 1050]
}

# =====================================================
# 3. COBERTURA Y POTABILIDAD
# =====================================================

datos_cobertura_potable = {
    "Aguascalientes": (98.8, "Buena (Tratamiento constante)"),
    "Baja California": (97.5, "Aceptable / Alta salinidad en zonas"),
    "Baja California Sur": (95.2, "Moderada (Presencia de minerales)"),
    "Campeche": (89.4, "Aceptable"),
    "Chiapas": (88.8, "Baja en zonas rurales (Riesgo biológico)"),
    "Chihuahua": (98.1, "Buena (Monitoreo continuo de arsénico)"),
    "Ciudad de México": (98.6, "Variable según alcaldía / Red antigua"),
    "Coahuila": (98.4, "Aceptable"),
    "Colima": (99.0, "Buena"),
    "Durango": (96.5, "Moderada (Presencia de flúor en acuíferos)"),
    "Estado de México": (96.2, "Variable / Presión intermitente"),
    "Guanajuato": (96.8, "Moderada (Tratamiento por metales en pozos)"),
    "Guerrero": (87.1, "Baja (Deficiencia en cloración municipal)"),
    "Hidalgo": (94.3, "Aceptable"),
    "Jalisco": (97.8, "Buena en ZMM / Variable en zonas agrícolas"),
    "Michoacán": (94.6, "Aceptable"),
    "Morelos": (96.9, "Buena"),
    "Nayarit": (95.1, "Aceptable"),
    "Nuevo León": (98.9, "Alta (Red automatizada y potabilizada)"),
    "Oaxaca": (85.5, "Baja en zonas rurales (Falta de potabilizadoras)"),
    "Puebla": (94.8, "Aceptable / Vulnerable en cuencas urbanas"),
    "Querétaro": (97.3, "Buena"),
    "Quintana Roo": (97.1, "Aceptable (Monitoreo de dureza y karst)"),
    "San Luis Potosí": (94.2, "Moderada"),
    "Sinaloa": (96.7, "Aceptable"),
    "Sonora": (97.6, "Moderada (Tratamiento en acuíferos del norte)"),
    "Tabasco": (88.2, "Moderada (Alta turbiedad en ríos)"),
    "Tamaulipas": (96.1, "Aceptable"),
    "Tlaxcala": (97.9, "Aceptable"),
    "Veracruz": (89.7, "Variable (Vulnerable a eventos meteorológicos)"),
    "Yucatán": (96.8, "Moderada (Agua dura / Acuífero somero)"),
    "Zacatecas": (96.3, "Moderada")
}

datos_consumo_agua = {
    "Aguascalientes": 680, "Baja California": 2950, "Baja California Sur": 480,
    "Campeche": 510, "Chiapas": 2400, "Chihuahua": 5150,
    "Ciudad de México": 1150, "Coahuila": 2350, "Colima": 1520,
    "Durango": 2100, "Estado de México": 2450, "Guanajuato": 4450,
    "Guerrero": 1350, "Hidalgo": 2650, "Jalisco": 4850,
    "Michoacán": 5400, "Morelos": 1320, "Nayarit": 1680,
    "Nuevo León": 2150, "Oaxaca": 1580, "Puebla": 2250,
    "Querétaro": 1050, "Quintana Roo": 790, "San Luis Potosí": 1540,
    "Sinaloa": 9850, "Sonora": 7200, "Tabasco": 690,
    "Tamaulipas": 4300, "Tlaxcala": 360, "Veracruz": 5600,
    "Yucatán": 1480, "Zacatecas": 1750
}

tab1, tab2, tab3 = st.tabs([
    "🗺️ Situación por Estado",
    "🚿 Simulador y Ahorro",
    "🎯 Quiz y Compromiso",
    "📚 Fuentes"
])
with tab1:
# =====================================================
# 4. CONSULTA POR ESTADO Y PERIODO
# =====================================================

st.subheader("1. Consulta por Estado y Periodo")

col1, col2 = st.columns(2)

with col1:
    estado_seleccionado = st.selectbox(
        "Selecciona un estado de México:",
        estados_mexico,
        index=estados_mexico.index("Chihuahua")
    )

with col2:
    periodo_seleccionado = st.selectbox(
        "Selecciona el periodo:",
        periodos,
        index=3
    )

st.subheader("2. Indicadores Hídricos y Potabilidad")

indice_periodo = periodos.index(periodo_seleccionado)

if indice_periodo < 4:
    disp_valor = datos_disponibilidad[estado_seleccionado][indice_periodo]
elif indice_periodo == 5:
    disp_valor = datos_disponibilidad[estado_seleccionado][3]
else:
    disp_valor = "Sin datos disponibles"

cobertura, potabilidad = datos_cobertura_potable[estado_seleccionado]

col_m1, col_m2, col_m3 = st.columns(3)

with col_m1:
    if isinstance(disp_valor, (int, float)):
        st.metric(
            f"Disponibilidad per cápita ({periodo_seleccionado})",
            f"{disp_valor:,.0f} m³/hab/año"
        )
    else:
        st.metric("Disponibilidad per cápita", disp_valor)

with col_m2:
    st.metric("Cobertura de Agua Entubada", f"{cobertura}%")

with col_m3:
    st.metric("Calidad / Potabilidad General", potabilidad)

st.info("""
💡 **¿Qué significan estos indicadores?**

- **Disponibilidad:** Mide el agua renovable total del estado dividida entre su población.
- **Cobertura:** Porcentaje de hogares conectados a la red pública de agua entubada.
- **Potabilidad:** Diagnóstico regional sobre la aptitud del agua para uso doméstico y los retos de tratamiento local.
""")

# =====================================================
# MAPA DE CONSUMO POR ESTADO (ENTRE SECCIÓN 2 Y 3)
# =====================================================

st.markdown("---")
st.subheader("🗺️ Mapa de Consumo Total de Agua por Estado")

st.write(
    "Visualización geográfica del volumen anual de agua concesionada/consumida "
    "(incluye uso agrícola, público e industrial en millones de m³)."
)

df_mapa = pd.DataFrame({
    "Estado": list(datos_consumo_agua.keys()),
    "Consumo_Mm3": list(datos_consumo_agua.values())
})

url_geojson = "https://raw.githubusercontent.com/angelnmara/geojson/master/mexicoHigh.json"
mapa_mexico = alt.Data(
    url=url_geojson,
    format=alt.DataFormat(property="features", type="json")
)

fig_mapa = alt.Chart(mapa_mexico).mark_geoshape(
    stroke="white",
    strokeWidth=0.8
).encode(
    color=alt.Color(
        "Consumo_Mm3:Q",
        scale=alt.Scale(scheme="blues"),
        title="Consumo (Mm³/año)"
    ),
    tooltip=[
        alt.Tooltip("properties.name:N", title="Estado"),
        alt.Tooltip("Consumo_Mm3:Q", title="Consumo (Mm³/año)", format=",.0f")
    ]
).transform_lookup(
    lookup="properties.name",
    from_=alt.LookupData(df_mapa, "Estado", ["Consumo_Mm3"])
).project(
    type="mercator"
).properties(
    width=650,
    height=420
).configure_view(
    strokeWidth=0
).configure(
    background="#F4F8FB"
)

st.altair_chart(fig_mapa, use_container_width=True)

estado_max = df_mapa.loc[df_mapa["Consumo_Mm3"].idxmax()]
consumo_sel = datos_consumo_agua.get(estado_seleccionado, 0)

st.warning(
    f"📊 **Mayor consumidor a nivel nacional:** **{estado_max['Estado']}** con aproximadamente "
    f"**{estado_max['Consumo_Mm3']:,.0f} millones de m³ al año** (impulsado principalmente por su alta actividad agrícola).\n\n"
    f"📍 **Estado seleccionado ({estado_seleccionado}):** Consume aproximadamente **{consumo_sel:,.0f} millones de m³ al año**."
)

# =====================================================
# 5. SIMULADOR DE DESPERDICIO
# =====================================================

st.markdown("---")
st.subheader("3. Simulador de desperdicio en botellas de agua (500 ml)")

st.write(
    "Calcula cuántas botellas de 500 ml y cuánto dinero equivaldría "
    "el agua que se desperdicia mientras esperas a que salga caliente "
    "en la regadera."
)

col_s1, col_s2, col_s3, col_s4 = st.columns(4)

with col_s1:
    minutos_espera = st.slider("Minutos de espera:", 0, 10, 3)

with col_s2:
    caudal_regadera = st.slider("Caudal (Litros/min):", 2, 15, 8)

with col_s3:
    frecuencia_semanal = st.slider("Duchas por semana:", 1, 14, 7)

with col_s4:
    costo_botella = st.number_input(
        "Precio por botella de 500 ml ($ MXN):",
        value=15.0,
        step=1.0
    )

litros_ducha = minutos_espera * caudal_regadera
litros_semana = litros_ducha * frecuencia_semanal
litros_mes = litros_semana * 4.33
litros_anio = litros_semana * 52

botellas_ducha = litros_ducha * 2
botellas_semana = litros_semana * 2
botellas_mes = litros_mes * 2
botellas_anio = litros_anio * 2

costo_ducha = botellas_ducha * costo_botella
costo_semana = botellas_semana * costo_botella
costo_mes = botellas_mes * costo_botella
costo_anio = botellas_anio * costo_botella

st.write("### 🫗 Impacto económico y volumen desperdiciado")

res_col1, res_col2, res_col3, res_col4 = st.columns(4)

with res_col1:
    st.metric(
        "Por ducha",
        f"${costo_ducha:,.2f} MXN",
        f"{int(botellas_ducha):,} botellas"
    )

with res_col2:
    st.metric(
        "Por semana",
        f"${costo_semana:,.2f} MXN",
        f"{int(botellas_semana):,} botellas"
    )

with res_col3:
    st.metric(
        "Por mes",
        f"${costo_mes:,.2f} MXN",
        f"{int(botellas_mes):,} botellas"
    )

with res_col4:
    st.metric(
        "Por año",
        f"${costo_anio:,.2f} MXN",
        f"{int(botellas_anio):,} botellas"
    )

# =====================================================
# 5.1 GRÁFICA MEJORADA
# =====================================================

if botellas_anio > 0:

    df_grafica = pd.DataFrame({
        "Periodo": ["Por ducha", "Por semana", "Por mes", "Por año"],
        "Costo_MXN": [
            costo_ducha,
            costo_semana,
            costo_mes,
            costo_anio
        ],
        "Botellas": [
            botellas_ducha,
            botellas_semana,
            botellas_mes,
            botellas_anio
        ]
    })

    st.markdown("### 📊 Comparación del desperdicio")
    st.caption(
        "Costo equivalente del agua desperdiciada según el periodo."
    )

    chart = alt.Chart(df_grafica).mark_bar(
        cornerRadiusEnd=8,
        height=28,
        color="#62B6CB"
    ).encode(
        x=alt.X(
            "Costo_MXN:Q",
            scale=alt.Scale(type="symlog"),
            axis=alt.Axis(
                title="Costo equivalente (MXN)",
                grid=True,
                format="$,.0f"
            )
        ),
        y=alt.Y(
            "Periodo:N",
            sort=["Por ducha", "Por semana", "Por mes", "Por año"],
            axis=alt.Axis(
                title=None,
                labelFontSize=12
            )
        ),
        tooltip=[
            alt.Tooltip("Periodo:N", title="Periodo"),
            alt.Tooltip(
                "Costo_MXN:Q",
                title="Costo",
                format="$,.2f"
            ),
            alt.Tooltip(
                "Botellas:Q",
                title="Botellas de 500 ml",
                format=",.0f"
            )
        ]
    ).properties(
        height=190
    ).configure_view(
        strokeWidth=0
    ).configure_axis(
        gridColor="#E5EDF2",
        domain=False,
        tickColor="#E5EDF2"
    ).configure(
        background="#F4F8FB"
    )

    st.altair_chart(
        chart,
        use_container_width=True
    )

st.caption(
    "Nota metodológica: cada litro equivale a dos botellas de 500 ml. "
    "El costo es una equivalencia ilustrativa, no una pérdida monetaria real."
)

# =====================================================
# 6. RECOMENDACIONES
# =====================================================

st.subheader("4. Recomendaciones prácticas")

st.success("""
**Acciones sencillas para reducir el consumo en casa:**

1. **Prepara todo antes de abrir la llave:** Ten listas tus cosas para evitar dejar la regadera abierta.
2. **Recolecta el agua fría inicial:** Coloca una cubeta mientras esperas el agua caliente y reutilízala en otras tareas.
3. **Instala ahorradores:** Las regaderas de bajo flujo pueden reducir el consumo.
4. **Repara fugas:** Evita que el agua se pierda continuamente.
""")

# =====================================================
# 7. RETO INTERACTIVO DE AHORRO
# =====================================================

st.markdown("---")

st.subheader("5. 💧 Reto interactivo: ¿cuánta agua puedes ahorrar?")

st.write(
    "Personaliza tus hábitos y descubre cuántos litros de agua "
    "podrías ahorrar al reducir el tiempo de ducha."
)

st.markdown("#### 🌱 Personaliza tu reto")

col_r1, col_r2 = st.columns(2)

with col_r1:
    minutos_reducidos = st.slider(
        "¿Cuántos minutos menos duraría tu ducha?",
        0, 10, 2,
        key="minutos_reto"
    )

    caudal_reto = st.slider(
        "Caudal de tu regadera (litros/minuto):",
        2, 15, 8,
        key="caudal_reto"
    )

with col_r2:
    dias_reto = st.slider(
        "¿Cuántos días por semana aplicarías el cambio?",
        1, 7, 7,
        key="dias_reto"
    )

    personas = st.number_input(
        "¿Cuántas personas participarían?",
        min_value=1,
        max_value=10,
        value=1,
        step=1
    )

litros_ahorrados_dia = (
    minutos_reducidos * caudal_reto * personas
)

litros_ahorrados_semana = (
    litros_ahorrados_dia * dias_reto
)

litros_ahorrados_mes = litros_ahorrados_semana * 4.33

st.markdown("#### 📊 Tu ahorro estimado")

m1, m2, m3 = st.columns(3)

m1.metric(
    "En un día",
    f"{litros_ahorrados_dia:,.1f} L"
)

m2.metric(
    "En una semana",
    f"{litros_ahorrados_semana:,.1f} L"
)

m3.metric(
    "En un mes",
    f"{litros_ahorrados_mes:,.1f} L"
)

st.progress(
    min(litros_ahorrados_semana / 500, 1.0)
)

st.caption(
    "La barra alcanza el 100 % al llegar a 500 litros de ahorro semanal."
)

with st.expander("🧮 ¿Cómo se calcula?"):
    st.write(
        "Litros ahorrados = minutos reducidos × caudal de la regadera "
        "× personas × días de aplicación."
    )

    st.latex(
        r"\text{Ahorro semanal} = "
        r"\text{minutos} \times "
        r"\text{litros/minuto} \times "
        r"\text{personas} \times \text{días}"
    )

# =====================================================
# 8. JUEGO DE PREGUNTAS
# =====================================================
with tab2:
st.markdown("---")

st.subheader("6. 🎯 Juego rápido: demuestra cuánto sabes")

st.write(
    "Responde las siguientes preguntas y comprueba tus conocimientos "
    "sobre la disponibilidad, calidad y cuidado del agua."
)

preguntas = [
    {
        "pregunta": "¿Qué representa la disponibilidad de agua per cápita?",
        "opciones": [
            "El agua que sale diariamente de la llave de cada casa",
            "El agua renovable disponible dividida entre la población",
            "Únicamente el agua utilizada para beber"
        ],
        "respuesta": "El agua renovable disponible dividida entre la población",
        "explicacion": (
            "Es un indicador general; no significa que toda esa agua "
            "llegue directamente a los hogares."
        )
    },
    {
        "pregunta": "¿Qué significa la cobertura de agua entubada?",
        "opciones": [
            "El porcentaje de hogares conectados a la red pública",
            "La cantidad de lluvia de un estado",
            "El porcentaje de agua embotellada que se consume"
        ],
        "respuesta": "El porcentaje de hogares conectados a la red pública",
        "explicacion": (
            "La cobertura describe el acceso a la red de agua entubada."
        )
    },
    {
        "pregunta": (
            "Si una regadera tiene un caudal de 8 litros por minuto "
            "y permanece abierta 3 minutos, ¿cuánta agua utiliza?"
        ),
        "opciones": [
            "8 litros",
            "11 litros",
            "24 litros"
        ],
        "respuesta": "24 litros",
        "explicacion": (
            "Se multiplica el caudal por el tiempo: 8 × 3 = 24 litros."
        )
    },
    {
        "pregunta": (
            "¿Qué puedes hacer con el agua fría que sale mientras "
            "esperas la caliente?"
        ),
        "opciones": [
            "Dejarla correr por el drenaje",
            "Recolectarla y reutilizarla en otras tareas",
            "Añadirle jabón y desecharla"
        ],
        "respuesta": "Recolectarla y reutilizarla en otras tareas",
        "explicacion": (
            "Puede aprovecharse para limpiar patios o descargar "
            "el sanitario, según su calidad y uso."
        )
    },
    {
        "pregunta": "¿Cuál de estas acciones ayuda a reducir el desperdicio?",
        "opciones": [
            "Reparar las fugas",
            "Dejar la llave abierta al cepillarse",
            "Aumentar el tiempo de ducha"
        ],
        "respuesta": "Reparar las fugas",
        "explicacion": (
            "Reparar fugas evita que el agua se pierda continuamente."
        )
    }
]

respuestas_usuario = []

for i, pregunta in enumerate(preguntas):
    seleccion = st.radio(
        f"**{i + 1}. {pregunta['pregunta']}**",
        pregunta["opciones"],
        key=f"quiz_agua_{i}"
    )

    respuestas_usuario.append(seleccion)

if st.button("Revisar mis respuestas", type="primary"):

    aciertos = 0

    for i, pregunta in enumerate(preguntas):
        if respuestas_usuario[i] == pregunta["respuesta"]:
            aciertos += 1

    st.session_state["resultado_quiz_agua"] = aciertos

    for i, pregunta in enumerate(preguntas):

        if respuestas_usuario[i] == pregunta["respuesta"]:
            st.success(
                f"Pregunta {i + 1}: ¡Correcto! "
                f"{pregunta['explicacion']}"
            )
        else:
            st.error(
                f"Pregunta {i + 1}: La respuesta correcta es "
                f"**{pregunta['respuesta']}**. "
                f"{pregunta['explicacion']}"
            )

if "resultado_quiz_agua" in st.session_state:

    puntos = st.session_state["resultado_quiz_agua"]

    st.metric(
        "Tu puntuación",
        f"{puntos} de {len(preguntas)}"
    )

    if puntos == len(preguntas):
        st.balloons()
        st.success(
            "¡Excelente! Tienes muy claros los conceptos."
        )

    elif puntos >= 3:
        st.info(
            "¡Buen trabajo! Repasa las explicaciones para mejorar."
        )

    else:
        st.info(
            "Sigue aprendiendo: revisa los indicadores y "
            "las recomendaciones de la aplicación."
        )

# =====================================================
# 9. COMPROMISO PERSONAL
# =====================================================

st.markdown("---")

st.subheader("7. ✍️ Mi compromiso con el agua")

st.write(
    "Marca las acciones que te comprometes a intentar "
    "durante esta semana."
)

compromisos = [
    "Cerrar la llave mientras me cepillo los dientes.",
    "Reducir el tiempo que paso en la ducha.",
    "Reutilizar agua cuando sea posible y seguro.",
    "Avisar si encuentro una fuga.",
    "Usar la lavadora con cargas completas."
]

acciones_realizadas = []

for i, compromiso in enumerate(compromisos):

    if st.checkbox(
        compromiso,
        key=f"compromiso_agua_{i}"
    ):
        acciones_realizadas.append(compromiso)

total_acciones = len(compromisos)
acciones_completadas = len(acciones_realizadas)

st.write(
    f"**Avance de tu compromiso: "
    f"{acciones_completadas} de {total_acciones} acciones**"
)

st.progress(
    acciones_completadas / total_acciones
)

if acciones_completadas == total_acciones:

    st.success(
        "¡Excelente compromiso! Cada hábito responsable "
        "ayuda a cuidar el agua."
    )

elif acciones_completadas > 0:

    st.info(
        "¡Vas avanzando! Mantén estas acciones durante la semana."
    )

else:

    st.caption(
        "Selecciona al menos una acción para comenzar tu compromiso."
    )

# =====================================================
# 10. FUENTES DE INFORMACIÓN
# =====================================================
with tab3:
st.markdown("---")

st.subheader("8. Fuentes de información oficiales")

st.write(
    "Para garantizar la veracidad del proyecto, los datos e "
    "indicadores se relacionan con las siguientes instituciones:"
)

st.markdown("""
### CONAGUA
Comisión Nacional del Agua.

Documento: Estadísticas del Agua en México / Atlas del Agua.

[Consultar CONAGUA](https://www.gob.mx/conagua)

### Sistema de Información del Agua

Institución: CONAGUA.

[Consultar Sistema de Información del Agua](https://app.conagua.gob.mx/sistemasdeagua/)

### INEGI

Instituto Nacional de Estadística y Geografía.

Documento: Censos de población y estadísticas sobre servicios de agua potable y saneamiento.

[Consultar INEGI](https://www.inegi.org.mx/)

### FAO

Organización de las Naciones Unidas para la Alimentación y la Agricultura.

Documento: Base de datos global de recursos hídricos (AQUASTAT).

[Consultar FAO](https://www.fao.org/)
""")

# =====================================================
# FINAL
# =====================================================

st.markdown("---")

st.title("💧 ¡Cada gota cuenta!")

st.write(
    "El cuidado del agua es una responsabilidad compartida. "
    "Conocer su disponibilidad, evitar el desperdicio y cambiar "
    "nuestros hábitos son pasos importantes para proteger este "
    "recurso indispensable."
)

st.success(
    "Gracias por participar y contribuir al cuidado del agua."
)
