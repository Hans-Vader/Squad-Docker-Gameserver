############################################################
# Dockerfile that builds a Squad Gameserver
############################################################
FROM cm2network/steamcmd:steam-bullseye

# Host account is not 1000:1000? Build with --build-arg PUID=$(id -u) --build-arg PGID=$(id -g),
# then the bind-mounted data dir stays owned by you and needs no sudo to edit.
ARG PUID=1000
ARG PGID=1000
USER root
RUN if [ "${PUID}:${PGID}" != "1000:1000" ]; then \
		groupmod -o -g "${PGID}" "${USER}" && \
		usermod -o -u "${PUID}" -g "${PGID}" "${USER}" && \
		chown -R "${PUID}:${PGID}" "${HOMEDIR}"; \
	fi
USER ${USER}

ENV STEAMAPPID 403240
ENV STEAMAPP squad
ENV STEAMAPPDIR "${HOMEDIR}/${STEAMAPP}-dedicated"

ENV STEAM_BETA_APP 774961
ENV STEAM_BETA_PASSWORD ""
ENV STEAM_BETA_BRANCH ""

ENV WORKSHOPID 393380
ENV MODPATH "${STEAMAPPDIR}/SquadGame/Plugins/Mods"
ENV MODS "()"

COPY --chown=${USER}:${USER} etc/entry.sh ${HOMEDIR}

RUN chmod 755 "${HOMEDIR}/entry.sh" && mkdir -p "${STEAMAPPDIR}"

ENV PORT=7787 \
	QUERYPORT=27165 \
	BEACONPORT=15000 \
	RCONPORT=21114 \
	FIXEDMAXPLAYERS=100 \
	FIXEDMAXTICKRATE=64 \
	RANDOM=NONE

WORKDIR ${HOMEDIR}

CMD ["bash", "entry.sh"]

# Expose ports
EXPOSE 7787/udp \
	15000/udp \
	27165/udp \
	21114/tcp \
