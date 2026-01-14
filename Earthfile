VERSION 0.8

IMPORT github.com/Konubinix/Earthfile AS e

SET_OPTION:
    FUNCTION
    ARG --required option
    ARG --required value
    # sanity check
    RUN grep -q "^#\?${option}" /etc/ssh/sshd_config
    RUN sed -r -i -e "s/^#?${option}.+/${option} ${value}/" /etc/ssh/sshd_config


build:
    FROM e+alpine --extra_packages="openssh-server rrsync rsync sed"

    ENV SSHD_HOST_KEYS_DIR /etc/ssh/host_keys
    VOLUME $SSHD_HOST_KEYS_DIR

    FOR option IN PermitRootLogin PasswordAuthentication StrictModes AllowAgentForwarding AllowTcpForwarding GatewayPorts X11Forwarding PermitUserEnvironment PermitTTY PrintMotd
        DO +SET_OPTION --option "${option}" --value "no"
    END

    FOR option IN PubkeyAuthentication
        DO +SET_OPTION --option "${option}" --value "yes"
    END

    RUN echo "ForceCommand /usr/bin/rrsync /data" >> /etc/ssh/sshd_config

    ENV USERS ""

    EXPOSE 22/tcp

    COPY entrypoint.sh /
    ENTRYPOINT ["/entrypoint.sh"]

    CMD ["/usr/sbin/sshd", "-D", "-e"]
