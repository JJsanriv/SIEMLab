Proyecto: Laboratorio SOC & Respuesta ante Incidentes con Wazuh
🛡️ Descripción del Proyecto

Implementación de un entorno de Security Operations Center (SOC) para la detección, monitorización y respuesta automatizada ante ciberataques. El laboratorio simula un entorno de producción donde un servidor (Ubuntu) es monitorizado por un SIEM (Wazuh) para detectar y mitigar amenazas en tiempo real.

🛠️ Stack Tecnológico

    SIEM: Wazuh (Manager, Indexer, Dashboard).

    SO Víctima: Ubuntu Server 22.04 LTS.

    SO Atacante: Kali Linux.

    Servicios Monitorizados: OpenSSH, Apache Web Server.

    Herramientas de Ataque: Hydra, Gobuster.

🏗️ Arquitectura del Laboratorio


🔍 Casos de Uso (Simulación de Ataques)
1. Detección y Mitigación de Fuerza Bruta (SSH)

    Ataque: Ejecución de ataque de diccionario contra el puerto 22 usando hydra.

    Detección: Configuración de reglas de Wazuh (ID 5710) para identificar múltiples fallos de autenticación.

    Respuesta (SOAR): Implementación de Active Response que bloquea automáticamente la IP del atacante en el Firewall (iptables) durante 60 segundos tras el tercer intento fallido.

2. Detección de Enumeración Web (Apache)

    Ataque: Escaneo de directorios ocultos y archivos sensibles utilizando gobuster.

    Detección: Ingesta y parseo de los logs de acceso de Apache (/var/log/apache2/access.log). Wazuh detecta un comportamiento anómalo (múltiples errores 404 en poco tiempo) y genera una alerta de nivel 7.

⚙️ Ingeniería de Detección y Respuesta (Blue Team)
Configuración del Active Response

Para automatizar la mitigación, se configuró el módulo de respuesta activa en el agente.

    Archivo modificado: /var/ossec/etc/ossec.conf

    Acción: Bloqueo dinámico mediante firewall-drop.

code Xml

<!-- Ejemplo de configuración implementada -->
<active-response>
  <command>firewall-drop</command>
  <location>local</location>
  <rules_id>5710</rules_id>
  <timeout>60</timeout>
</active-response>

📈 Resultados y Evidencias

    Dashboard: Se visualizan los eventos críticos categorizados por la matriz MITRE ATT&CK.

    Automatización: Se verificó la integridad del bloqueo mediante el log active-responses.log y la inspección de las reglas de iptables en el servidor víctima.

🚀 Aprendizajes Clave

    Ingeniería de Detección: Creación de reglas personalizadas en XML para categorizar amenazas específicas.

    Visibilidad: Gestión de logs y parseo de servicios críticos (SSH, Apache).

    Mitigación: Reducción de la ventana de exposición mediante la automatización de bloqueos de red (Active Response).

💡 Próximos pasos

    🔜 Implementar FIM (File Integrity Monitoring) para detectar cambios en archivos críticos de configuración web. 

    🔜 Integración de alertas mediante Telegram o correo electrónico.

    🔜 Análisis de vulnerabilidades usando el escáner de vulnerabilidades integrado en Wazuh.
