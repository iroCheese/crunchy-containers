ARG BASEOS
ARG BASEVER
ARG PG_FULL
ARG PREFIX
FROM ${PREFIX}/crunchy-pg-base:${BASEOS}-${PG_FULL}-${BASEVER}

# For RHEL8 all arguments used in main code has to be specified after FROM
ARG DFSET
ARG PACKAGER

ARG PG_MAJOR

RUN ${PACKAGER} -y install \
	--setopt=skip_missing_names_on_install=False \
	file \
	postgresql${PG_MAJOR//.}-server \
	unzip \
	&& ${PACKAGER} -y clean all

LABEL name="pgdump" \
	summary="Performs a pg_dump on a database container" \
	description="Meant to be executed upon demand, this container will run pg_dump against a running database container and write the backup files to a mounted directory." \
	io.k8s.description="pgdump container" \
	io.k8s.display-name="Crunchy pgdump container" \
	io.openshift.tags="postgresql,postgres,pgdump,database,crunchy"

# Preserving PGVERSION out of paranoia
ENV PGVERSION="${PG_MAJOR}"

RUN mkdir -p /opt/cpm/bin /opt/cpm/conf /pgdata
ADD bin/pgdump/ /opt/cpm/bin
ADD bin/common /opt/cpm/bin
ADD conf/pgdump/ /opt/cpm/conf

RUN chgrp -R 0 /opt/cpm /pgdata && \
	chmod -R g=u /opt/cpm /pgdata

RUN chmod g=u /etc/passwd && \
	chmod g=u /etc/group

# The VOLUME directive must appear after all RUN directives to ensure the proper
# volume permissions are applied when building the image
VOLUME ["/pgdata"]

ENTRYPOINT ["opt/cpm/bin/uid_postgres.sh"]

USER 26

CMD ["/opt/cpm/bin/start.sh"]
