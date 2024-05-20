# ansible-troubleshooting
El mensaje de error indica que la tarea para cambiar la contraseña de administrador de Grafana utilizando el comando grafana-cli falló. Para solucionar este problema, puedes seguir estos pasos:

1. Verificar la Instalación del CLI de Grafana: Asegúrate de que la herramienta grafana-cli esté instalada en la máquina virtual y sea accesible en el PATH del sistema.
```
which grafana-cli
```
Si grafana-cli no se encuentra, necesitas instalar Grafana o asegurarte de que el directorio de instalación esté incluido en el PATH.

2.Verificar el Estado del Servicio de Grafana: Asegúrate de que el servicio de Grafana esté en ejecución. El comando grafana-cli puede fallar si Grafana no está corriendo.
```
sudo systemctl status grafana-server
```
Si el servicio no está en ejecución, inícialo:
```
sudo systemctl start grafana-server
```
3.Ejecutar el Comando Manualmente: Intenta ejecutar el comando manualmente en la máquina virtual para ver si funciona fuera de Ansible.
```
sudo grafana-cli admin reset-admin-password mipassword
```
Si este comando falla, puede proporcionar mensajes de error más específicos que ayuden a diagnosticar el problema.

4. Verificar Permisos: Asegúrate de que el usuario que ejecuta el playbook de Ansible tenga los permisos necesarios para ejecutar grafana-cli. Puede que necesites usar sudo en tu tarea de Ansible.

5. Actualizar el Playbook de Ansible: Modifica tu tarea de Ansible para incluir become: yes y así ejecutar el comando con privilegios elevados.
```
- name: change admin password for grafana GUI
  become: yes
  command: grafana-cli admin reset-admin-password mypassword
  register: result
- debug:
    var: result
```


6. Revisar la Salida de Ansible: La salida en la tarea de depuración puede proporcionar más información sobre por qué el comando está fallando.
Aquí tienes un fragmento de playbook de ejemplo con estos cambios:
```
- hosts: all
  tasks:
    - name: Ensure Grafana is running
      become: yes
      systemd:
        name: grafana-server
        state: started
        enabled: yes

    - name: Change admin password for Grafana GUI
      become: yes
      command: graf
```
Aquí tienes un fragmento de playbook de ejemplo con estos cambios:

```
- hosts: all
  tasks:
    - name: Ensure Grafana is running
      become: yes
      systemd:
        name: grafana-server
        state: started
        enabled: yes

    - name: Change admin password for Grafana GUI
      become: yes
      command: grafana-cli admin reset-admin-password mypassword
      register: result

    - debug:
        var: result
```
Este playbook asegura que el servicio de Grafana esté en ejecución e intenta cambiar la contraseña del administrador con privilegios elevados. También incluye una tarea de depuración para mostrar el resultado del comando, lo cual puede ayudarte a diagnosticar el problema si el comando sigue fallando.



Si el problema persiste, revisa los siguientes puntos:
1.Logs de Grafana: Revisa los logs de Grafana para cualquier mensaje de error relacionado. Los logs generalmente se encuentran en /var/log/grafana/grafana.log.

2.Logs de Ansible: Aumenta la verbosidad de tu comando de Ansible para obtener más detalles sobre el fallo.
```
ansible-playbook -i inventario tu_playbook.yml -vvv
```
3.Variables de Entorno: A veces, las variables de entorno pueden afectar la ejecución de grafana-cli. Asegúrate de que cualquier variable de entorno necesaria esté configurada correctamente dentro de la tarea de Ansible.
