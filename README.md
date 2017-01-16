# Shell function to clean docker stuff

La siguiente es una funcion 'shell' que agrupa varias funcionalidades para limpiar elementos remanentes de ejecuciones de docker.

Una vez instalada, solo hace falta reiniciar la terminal y ejecutar el comando `docker-clean`.


```shell

$ cat << 'EOF' >> ~/.bashrc

function docker-clean(){
    local _ELEMENT_="${1}"
    local _ID_="${2}"

    case ${_ELEMENT_} in
 containers ) local _COMMAND_="docker rm -f" ;;
     images ) local _COMMAND_="docker rmi -f" ;;
     volume ) local _COMMAND_="docker volume rm" ;;
          * ) echo "ERROR: You must use images/containers/volumes as first parameter"
    esac

    case ${_ID_} in
        all ) case ${_ELEMENT_} in
     containers ) local _PARAMS_=( $(docker ps -qa) ) ;;
         images ) local _PARAMS_=( $(docker images -q) ) ;;
         volume ) local _PARAMS_=( $(docker volume ls -q) ) ;;
              esac
             ;;
   dangling ) case ${_ELEMENT_} in
     containers ) local _PARAMS_=( $(docker ps -qa --filter 'status=exited' --filter 'status=created') ) ;;
         images ) local _PARAMS_=( $(docker images -q --filter "dangling=true") ) ;;
         volume ) local _PARAMS_=( $(docker volume ls -q --filter 'dangling=true') ) ;;
              esac
             ;;
         '' ) echo "ERROR: You must use all/dangling/<id>/<name> as second parameter";;
          * ) local _PARAMS_=( ${_ID_} ) ;;
    esac
    
    [[ -z "${_PARAMS_}" ]] && return 0

    for item in ${_PARAMS_[*]}; do
        eval "${_COMMAND_} ${item}"
    done
}
EOF
```
