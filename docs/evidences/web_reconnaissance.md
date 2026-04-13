## **Caso de Estudio: Detección y Mitigación de Escaneo de Directorios (Web Reconnaissance)**
### **1. Vector de Ataque**

    Tipo: Enumeración de directorios y archivos (Web Reconnaissance).

    Herramienta: dirb.

    Objetivo: Servidor Apache (http://10.0.2.4).

### **2. Procedimiento**

Se realizó una simulación de fase de reconocimiento contra el servidor web Apache utilizando la herramienta dirb. El atacante intentó enumerar directorios ocultos y archivos sensibles mediante fuerza bruta contra la estructura de directorios del servidor, generando un alto volumen de errores HTTP 404 (Not Found).

Comando ejecutado:
```dirb http://10.0.2.4```

<img width="1920" height="955" alt="image" src="https://github.com/user-attachments/assets/71e248c7-1bcc-42ed-a693-a96462ede641" />

### **3. Detección (Wazuh SIEM)**

Wazuh monitorizó los logs de acceso de Apache (/var/log/apache2/access.log). El sistema identificó rápidamente una tasa anormal de errores 404 provenientes de la misma dirección IP (10.0.2.3), lo cual es un indicador claro de una herramienta de escaneo automatizado.

<img width="1920" height="955" alt="image" src="https://github.com/user-attachments/assets/ddd88c6c-9204-450d-9b6b-050ced8e84ff" />

Detalles del evento:

    Rule ID: 31101 (Web server 400 error code) y 31151 (Multiple web server 400 error codes from same source ip).

    Criticidad: Elevada (Nivel 10 para la regla 31151).

    Agente: Ubuntu-server.

### **4. Análisis y Conclusiones**

    Análisis: El agente de Wazuh analizó la secuencia de logs y detectó el patrón de enumeración. La regla 31151 se disparó tras identificar un volumen crítico de errores de cliente (4xx) en un corto periodo de tiempo, clasificando la actividad como malintencionada.

    Mitigación: Al igual que en el caso anterior, el framework de Active Response reaccionó automáticamente. El log muestra la ejecución del comando firewall-drop para aislar al atacante.

    Evidencia de Bloqueo: En el dashboard se registró el evento "Host Blocked by firewall-drop Active Response" (Rule ID 651), confirmando que el acceso desde 10.0.2.3 fue restringido a nivel de red por el firewall. Posteriormente, se observa el log "Host Unblocked..." una vez finalizado el tiempo de espera configurado.

  **Conclusión**: Este escenario demuestra la capacidad de Wazuh para proteger no solo servicios de acceso remoto (SSH), sino también servicios web. La detección automática de escaneos de directorios permite detener a un atacante antes de que encuentre una vulnerabilidad real o un archivo sensible en la infraestructura web, reforzando la seguridad perimetral de la aplicación.
