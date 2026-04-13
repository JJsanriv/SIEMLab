## **Caso de Estudio: Detección y Mitigación de Fuerza Bruta SSH**

### **1. Vector de Ataque**

    Tipo: Fuerza Bruta (Brute Force Attack).

    Herramienta: Hydra.

    Objetivo: Servidor Ubuntu (IP: 10.0.2.4), servicio SSH (puerto 22).

### **2. Procedimiento**

Se simuló un escenario de ataque real lanzando un ataque de diccionario desde la máquina atacante Kali Linux (10.0.2.3) contra el servicio SSH del servidor víctima. El objetivo fue enumerar credenciales y obtener acceso no autorizado.

Comando ejecutado:
```hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://10.0.2.4```

### **3. Detección (Wazuh SIEM)**

El sistema Wazuh detectó inmediatamente una actividad inusual en el servicio SSH. La correlación de eventos generó alertas de nivel 5 vinculadas a intentos fallidos de autenticación (Regla ID 5710).

<img width="1920" height="955" alt="image" src="https://github.com/user-attachments/assets/5f672a42-abf1-4153-b92f-3051ba356ad7" />


Detalles del evento:

    Rule ID: 5710 (sshd: Attempt to login using a non-existent user).

    Agente: Ubuntu-server.

    Frecuencia: Se registraron cientos de intentos fallidos en un intervalo inferior a 1 minuto, lo cual activó los umbrales de alerta crítica del sistema.

### **4. Análisis y Conclusiones**

    Análisis: Los logs recopilados en /var/log/auth.log fueron analizados en tiempo real por el agente de Wazuh. La regla 5710 permitió clasificar el tráfico como malicioso basándose en el patrón de intentos de inicio de sesión fallidos contra usuarios no existentes.

    Mitigación: Una vez que el umbral de intentos fallidos fue superado, el módulo de Active Response ejecutó automáticamente el script firewall-drop.

    Resultado: La dirección IP del atacante (10.0.2.3) fue bloqueada instantáneamente en la cadena INPUT del firewall local (iptables), impidiendo que el ataque continuara y reduciendo la ventana de exposición a casi cero.

  **Conclusión**: La implementación de esta arquitectura permite pasar de una monitorización pasiva a una monitorización proactiva (SOAR), minimizando el riesgo de posibles ataques automatizados de fuerza bruta sin necesidad de intervención humana inmediata.
