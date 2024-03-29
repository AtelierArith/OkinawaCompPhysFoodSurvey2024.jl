FROM julia:1.10.1

# create user with a home directory
ARG NB_USER=jovyan
ARG NB_UID=1000
ENV USER ${NB_USER}
ENV HOME /home/${NB_USER}

RUN adduser --disabled-password \
    --gecos "Default user" \
    --uid ${NB_UID} \
    ${NB_USER}

USER root

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
    build-essential \
    git \
    unzip \
    wget \
    fontconfig \
    && \
    apt-get clean && rm -rf /var/cache/apt/archives/* /var/lib/apt/lists/* # clean up

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
    htop \
    nano \
    openssh-server \
    tig \
    tree \
    && \
    apt-get clean && rm -rf /var/cache/apt/archives/* /var/lib/apt/lists/* # clean up

USER ${USER}

WORKDIR /workspace/OkinawaCompPhysFoodSurvey2024.jl

RUN julia -e 'using Pkg; Pkg.add(["Revise", "LiveServer", "Pluto", "PlutoUI"])' && \
    julia -e 'using Pkg; Pkg.add(["BenchmarkTools", "ProfileSVG", "JET", "JuliaFormatter"])'


ENV JULIA_PROJECT "@."

COPY ./src /workspace/OkinawaCompPhysFoodSurvey2024.jl/src
COPY ./docs/Project.toml /workspace/OkinawaCompPhysFoodSurvey2024.jl/docs
COPY ./Project.toml /workspace/OkinawaCompPhysFoodSurvey2024.jl

USER root
RUN chown -R ${NB_UID} /workspace/OkinawaCompPhysFoodSurvey2024.jl
USER ${USER}

RUN rm -f Manifest.toml && julia -e 'using Pkg; \
    Pkg.instantiate(); \
    Pkg.precompile()' && \
    # Check Julia version \
    julia -e 'using InteractiveUtils; versioninfo()'

WORKDIR ${HOME}
USER ${USER}
EXPOSE 8000

CMD ["julia"]
