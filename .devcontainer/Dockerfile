######################################################################
# Development container image
#
# This Dockerfile builds the image used by the VS Code devcontainer.
# It extends the official devcontainers base image and installs a
# collection of common build tools, language runtimes and utilities.
#
# The image is pinned to a specific digest so that builds are
# reproducible. Feel free to add or remove packages to suit your
# project's needs.
######################################################################
FROM mcr.microsoft.com/devcontainers/base:latest AS base

# Override the default temporary directory if needed. Useful when
# working on filesystems where /tmp has limited space.
ARG TMPDIR=/tmp

# Locale and timezone settings used during build and at runtime
ARG LANG=en_US.UTF-8
ARG LANGUAGE=en_US:en
ARG TZ=UTC
ARG LC_ALL=en_US.UTF-8

ARG NODE_OPTIONS="--max-old-space-size=4096 --max-new-space-size=2048 --max-semi-space-size=512"

# ENV NODE_OPTIONS="--max-old-space-size=4096 --report-directory=$USER_SYS_REPORTS/node"

# Set workspace home directory
ENV WORKSPACE_HOME=/workspace

# Location where additional development utilities are installed
ENV UNIVERSAL_HOME=/opt/devtools
ENV UNIVERSAL_BIN="${UNIVERSAL_HOME}/bin"
ENV UNIVERSAL_TOOLBOX="${UNIVERSAL_HOME}/toolbox"
ENV UNIVERSAL_CACHE="${UNIVERSAL_HOME}/cache"
ENV UNIVERSAL_LOGS="${UNIVERSAL_HOME}/logs"
ENV UNIVERSAL_CONFIG="${UNIVERSAL_HOME}/config"
ENV UNIVERSAL_LIB="${UNIVERSAL_HOME}/lib"
ENV UNIVERSAL_LOCKS="${UNIVERSAL_HOME}/locks"
ENV UNIVERSAL_FONTS="${UNIVERSAL_HOME}/fonts"
ENV UNIVERSAL_RUNTIME="${UNIVERSAL_HOME}/runtime"
ENV UNIVERSAL_APPS="${UNIVERSAL_HOME}/apps"
ENV APT_CACHE_DIR="${UNIVERSAL_CACHE}/apt"

# Core environment variables for non-interactive apt operations and consistent Python behavior.
ENV DEBIAN_FRONTEND=noninteractive \
    DEBCONF_NONINTERACTIVE_SEEN=true \
    DEBIAN_PRIORITY=critical \
    DEBCONF_NOWARNINGS=yes \
    TERM=xterm-256color \
    APT_LISTCHANGES_FRONTEND=none \
    APT_LISTBUGS_FRONTEND=none \
    TMPDIR=${TMPDIR} \
    LANG=${LANG} \
    LANGUAGE=${LANGUAGE} \
    TZ=${TZ} \
    LC_ALL=${LC_ALL} \
    PYTHONUNBUFFERED=1 \
    PYTHONIOENCODING=UTF-8 \
    PYTHONUTF8=1 \
    PYTHONHASHSEED=random \
    PIP_NO_CACHE_DIR=1 \
    PYTHONFAULTHANDLER=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=on \
    PIP_NO_WARN_SCRIPT_LOCATION=on \
    PIP_DEFAULT_TIMEOUT=200 \
    PIP_ROOT_USER_ACTION=ignore \
    CARGO_TERM_COLOR=always \
    DO_NOT_TRACK=1 \
    PYTHONWARNINGS=default \
    CLICOLOR_FORCE=1 \
    CLICOLOR=1 \
    LESS=-R \
    COLORTERM=truecolor \
    GCC_COLORS="error=01;31:warning=01;35:note=01;36:caret=01;32:locus=01:quote=01" \
    FORCE_COLOR=1 \
    EDITOR=code \
    VISUAL=code \
    PAGER=less \
    GIT_PAGER=less

# XDG Base Directories: https://wiki.archlinux.org/title/XDG_Base_Directory
ENV XDG_CONFIG_HOME=${UNIVERSAL_CONFIG} \
    XDG_CACHE_HOME=${UNIVERSAL_CACHE} \
    XDG_DATA_HOME=${UNIVERSAL_TOOLBOX} \
    XDG_STATE_HOME=${UNIVERSAL_RUNTIME} \
    XDG_CONFIG_DIRS="${UNIVERSAL_CONFIG}:/etc/xdg"

# Add user application .desktop files to gnome.
# https://help.gnome.org/admin//system-admin-guide/2.32/menustructure-desktopentry.html.en
ENV XDG_DATA_DIRS="${UNIVERSAL_APPS}:/usr/local/share:/usr/share"

# Configure the ASDF version manager.
ENV ASDF_DIR="${UNIVERSAL_HOME}/.asdf"
ENV ASDF_DATA_DIR="${ASDF_DIR}/data"
ENV ASDF_SHIMS_DIR="${ASDF_DATA_DIR}/shims"

# Taskfile installation directory
ENV TASKFILE_HOME_DIR="${UNIVERSAL_HOME}/.task"

# Prepend devtool locations to the PATH for easy access
ENV PATH="${UNIVERSAL_BIN}:${UNIVERSAL_BIN}/install:${UNIVERSAL_BIN}/tests:${UNIVERSAL_TOOLBOX}:${TASKFILE_HOME_DIR}:${ASDF_DIR}/bin:${ASDF_SHIMS_DIR}:${PATH}"

# Set working directory
WORKDIR ${WORKSPACE_HOME}

# Copy custom shell scripts and binaries
COPY --chown=vscode:vscode shell/bin/ ${UNIVERSAL_BIN}/
RUN chmod +x ${UNIVERSAL_BIN}/*

# COPY custom shell library
COPY --chown=vscode:vscode shell/lib/ ${UNIVERSAL_LIB}/
RUN find ${UNIVERSAL_LIB} -type f -name "*.sh" -exec chmod +x {} \;

# Copy configuration files for apt and dpkg
COPY shell/etc/dpkg/dpkg.cfg.d/*.conf /etc/dpkg/dpkg.cfg.d/
COPY shell/etc/apt/apt.conf.d/*.conf /etc/apt/apt.conf.d/

# Copy configuration files
# COPY shell/config/.gitignore_global

FROM base AS fonts

# Optional Google Fonts installation
ARG INSTALL_GOOGLE_FONTS=false
ARG GOOGLE_FONTS_SHA_COMMIT=2b5bd4077bd9269cdf3114266603372af6c3222d
ARG GOOGLE_FONTS_SHA256=e413e29c18fa727ff4d509280fd432fc0ecb1f6117eb29f2c0a87918d30fe3ad

ENV INSTALL_GOOGLE_FONTS=${INSTALL_GOOGLE_FONTS} \
    GOOGLE_FONTS_SHA_COMMIT=${GOOGLE_FONTS_SHA_COMMIT} \
    GOOGLE_FONTS_SHA256=${GOOGLE_FONTS_SHA256}

RUN --mount=type=cache,target=/var/cache/fonts,sharing=locked,id=google-fonts \
    if [ "${INSTALL_GOOGLE_FONTS}" = "true" ]; then \
    chmod +x /workspace/scripts/install-google-fonts.sh && \
    bash /workspace/scripts/install-google-fonts.sh "/usr/local/share/fonts/google" && \
    fc-cache -f -v; \
    else \
    echo "📁 Skipping Google Fonts install"; \
    fi

FROM base AS imagemagick

# Optional ImageMagick installation
ARG INSTALL_IMAGEMAGICK=false
ARG IMAGEMAGICK_VERSION=7.1.1-47
ARG IMAGEMAGICK_SHA256=53ea43035cf0a1573bebd215932ea6ffeaa7ca1703f4e57d1e849b5bc0bed859
ARG IMAGEMAGICK_MD5=895118664de9d97a10c2d86e222c60c5

ENV INSTALL_IMAGEMAGICK=${INSTALL_IMAGEMAGICK} \
    IMAGEMAGICK_VERSION=${IMAGEMAGICK_VERSION} \
    IMAGEMAGICK_SHA256=${IMAGEMAGICK_SHA256} \
    IMAGEMAGICK_MD5=${IMAGEMAGICK_MD5} \
    IMAGEMAGICK_PREFIX=${UNIVERSAL_TOOLBOX}/imagemagick

ENV PATH="${IMAGEMAGICK_PREFIX}/bin:${PATH}" \
    PKG_CONFIG_PATH="${IMAGEMAGICK_PREFIX}/lib/pkgconfig:${PKG_CONFIG_PATH}" \
    LD_LIBRARY_PATH="${IMAGEMAGICK_PREFIX}/lib:${LD_LIBRARY_PATH}"

RUN if [ "${INSTALL_IMAGEMAGICK}" = "true" ]; then install-imagemagick; fi

FROM base AS texlive

# Optional TeX Live installation
ARG INSTALL_TEXLIVE=false

# Set texlive directories.
ENV TEXLIVE_INSTALL_PREFIX="${UNIVERSAL_TOOLBOX}/texlive"
ENV TEXMFHOME="${TEXLIVE_INSTALL_PREFIX}/texmf"
ENV TEXMFVAR="${TEXLIVE_INSTALL_PREFIX}/.texlive/texmf-var"
ENV TEXMFCONFIG="${TEXLIVE_INSTALL_PREFIX}/.texlive/texmf-config"
ENV INSTALL_TEXLIVE=${INSTALL_TEXLIVE}

# Optional TeX Live installation
RUN if [ "${INSTALL_TEXLIVE}" = "true" ]; then \
    apt-get install --yes --no-install-recommends texlive-full=2023.20240207-1; \
    else \
    echo "📁 Skipping texlive-full install"; \
    fi

FROM base AS dependencies

# Disable the automatic removal of downloaded packages
RUN rm -f /etc/apt/apt.conf.d/docker-clean

# Fix for update-alternatives: error:
# 'error creating symbolic link '/usr/share/man/man1/rmid.1.gz.dpkg-tmp': No such file or directory'
# See https://github.com/debuerreotype/docker-debian-artifacts/issues/24#issuecomment-360870939
RUN mkdir --parents /usr/share/man/man1

# Install common development tools and libraries
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked,id=apt-cache \
    --mount=type=cache,target=/var/lib/apt,sharing=locked,id=apt-lib \
    apt-get update -qq && \
    apt-get install --yes --no-install-recommends \
    ansible \
    apt-rdepends \
    bat \
    build-essential \
    ca-certificates \
    ccache \
    procps \
    file \
    chktex \
    clang-format \
    clang-tidy \
    clang \
    cmake \
    cpanminus \
    curl \
    debtree \
    dnsutils \
    dos2unix \
    ffmpeg \
    fontconfig \
    fonts-lmodern \
    fonts-noto-cjk \
    fonts-noto-color-emoji \
    fonts-noto \
    fzf \
    gdal-bin \
    gettext \
    ghostscript \
    git \
    gnupg \
    graphviz \
    htop \
    inkscape \
    iputils-ping \
    jq \
    less \
    libblosc-dev \
    libboost-all-dev \
    libbz2-dev \
    libcurl4-openssl-dev \
    libdb-dev \
    libev-dev \
    libffi-dev \
    libgdal-dev \
    libgdbm-dev \
    libgeos-dev \
    liblcms2-dev \
    liblz4-dev \
    liblzma-dev \
    libncurses-dev \
    libnss3-dev \
    libperl-dev \
    libproj-dev \
    libreadline-dev \
    libspatialindex-dev \
    libsqlite3-dev \
    libssl-dev \
    libxi-dev \
    libxml2-dev \
    libxmlsec1-dev \
    libxslt1-dev \
    libzstd-dev \
    llvm \
    locales-all \
    locales \
    logrotate \
    lsof \
    make \
    moreutils \
    nano \
    net-tools \
    netcat-openbsd \
    ninja-build \
    nmap \
    openssh-client \
    pandoc \
    pkg-config \
    proj-bin \
    pwgen \
    python3-dev \
    python3-pip \
    python3-venv \
    python3 \
    ripgrep \
    strace \
    sudo \
    sysdig \
    sysstat \
    tar \
    tcpdump \
    tk-dev \
    tzdata \
    unzip \
    uuid-dev \
    vim \
    wget \
    xz-utils \
    zlib1g-dev \
    zsh \
    libtool \
    potrace

# COPY --from=fonts /opt/devtools/fonts /opt/devtools/fonts
RUN fc-cache -f -v

# Configure the system locale and timezone
RUN echo "LC_ALL=en_US.UTF-8" >> /etc/environment \
    && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen \
    && echo "LANG=en_US.UTF-8" > /etc/locale.conf \
    && locale-gen en_US.UTF-8 \
    && update-locale LANG=en_US.UTF-8 \
    && dpkg-reconfigure locales

FROM dependencies AS perl

RUN apt-get update && apt-get install --yes \
  build-essential \
  perl \
  perl-base \
  perl-modules \
  libperl-dev \
  libio-aio-perl

# Install Perl modules needed for Perl Language Server
RUN cpanm --no-interactive AnyEvent::AIO IO::AIO Perl::LanguageServer

FROM perl AS devtools

# Copy .tool-versions file
COPY .tool-versions ${WORKSPACE_HOME}/.tool-versions

# Install asdf version manager and plugins along with taskfile
RUN /usr/bin/env bash ${UNIVERSAL_BIN}/setup-asdf

FROM devtools AS final

# Ensure SYS_UID_MAX and SYS_GID_MAX are set to high values, uncommented or inserted
RUN /usr/bin/env bash ${UNIVERSAL_BIN}/set-sys-uid-max

# Create the directory in advance so ownership can be adjusted later
RUN mkdir -p "${UNIVERSAL_HOME}"

# Copy and source custom shell aliases
COPY shell/aliases /etc/profile.d/aliases.sh

# Ensure correct permissions and sourcing
RUN chmod 644 /etc/profile.d/aliases.sh && \
    echo '[ -d /etc/profile.d ] && for f in /etc/profile.d/*.sh; do [ -r "$f" ] && . "$f"; done' \
    >> /etc/bash.bashrc

RUN echo 'source /etc/profile.d/aliases.sh' >> /etc/zsh/zshrc



FROM base AS environment

# Set cache directories for various tools.
ENV SONARLINT_USER_HOME="${XDG_CACHE_HOME}/.sonarlint" \
    YARN_CACHE_FOLDER="${XDG_CACHE_HOME}/yarn" \
    PUB_CACHE="${XDG_CACHE_HOME}/.pub-cache" \
    __GL_SHADER_DISK_CACHE_PATH="${XDG_CACHE_HOME}/.nv" \
    HOMEBREW_CACHE="${XDG_CACHE_HOME}/homebrew" \
    KSCRIPT_CACHE_DIR="${XDG_CACHE_HOME}/kscript" \
    PYTHON_EGG_CACHE="${XDG_CACHE_HOME}/.python-eggs" \
    PEX_ROOT="${XDG_CACHE_HOME}/.pex" \
    MYPY_CACHE_DIR="${XDG_CACHE_HOME}/mypy" \
    CUDA_CACHE_PATH="${XDG_CACHE_HOME}/nv" \
    CALIBRE_CACHE_DIRECTORY="${XDG_CACHE_HOME}/calibre" \
    SOLARGRAPH_CACHE="${XDG_CACHE_HOME}/solargraph" \
    XCOMPOSECACHE="${XDG_CACHE_HOME}/X11/xcompose" \
    DVDCSS_CACHE="${XDG_CACHE_HOME}/dvdcss" \
    XMONAD_CACHE_DIR="${XDG_CACHE_HOME}/xmonad" \
    BUNDLE_USER_CACHE="${XDG_CACHE_HOME}/bundle" \
    GEM_SPEC_CACHE="${XDG_CACHE_HOME}/gem" \
    ICEAUTHORITY="${XDG_CACHE_HOME}/ICEauthority" \
    PYLINTHOME="${XDG_CACHE_HOME}/pylint" \
    STARSHIP_CACHE="${XDG_CACHE_HOME}/starship" \
    ALTUSERXSESSION="${XDG_CACHE_HOME}/X11/Xsession" \
    USERXSESSION="${XDG_CACHE_HOME}/X11/xsession" \
    USERXSESSIONRC="${XDG_CACHE_HOME}/X11/xsessionrc" \
    SINGULARITY_CACHEDIR="${XDG_CACHE_HOME}/singularity" \
    RUFF_CACHE_DIR="${XDG_CACHE_HOME}/ruff" \
    EM_CACHE="$XDG_CACHE_HOME/emscripten/cache" \
    HOUDINI_USER_PREF_DIR="$XDG_CACHE_HOME/houdini__HVER__"

# History settings
ENV HISTFILE="${XDG_CACHE_HOME}/bash_history" \
    HISTSIZE=10000 \
    NODE_REPL_HISTORY="${XDG_CACHE_HOME}/node_repl_history" \
    LESSHISTFILE="${XDG_CACHE_HOME}/less_history" \
    LESSHISTSIZE=10000 \
    REDISCLI_HISTFILE="${XDG_CACHE_HOME}/rediscli_history" \
    SQLITE_HISTORY="${XDG_CACHE_HOME}/sqlite_history" \
    PSQL_HISTORY="${XDG_CACHE_HOME}/psql_history" \
    PGSQL_HISTORY="${XDG_CACHE_HOME}/pgsql_history" \
    MYSQL_HISTFILE="${XDG_CACHE_HOME}/mysql_history" \
    MYSQL_HISTSIZE=10000 \
    CALCHISTFILE="${XDG_CACHE_HOME}/calc_history" \
    UNITS_HISTORY_FILE="${XDG_CACHE_HOME}/units_history" \
    RLWRAP_HOME="${XDG_CACHE_HOME}/rlwrap" \
    GDBHISTFILE="${XDG_CACHE_HOME}/gdb_history" \
    OCTAVE_HISTFILE="${XDG_CACHE_HOME}/octave-hsts"

# Disable telemetry for various tools.
# https://github.com/beatcracker/toptout
ENV DO_NOT_TRACK=1 \
    ANALYTICS="no" \
    ADBLOCK="true" \
    DisableTelemetry="True" \
    TELEMETRY_ENABLED=0 \
    AZURE_CORE_COLLECT_TELEMETRY=0 \
    SAM_CLI_TELEMETRY=0 \
    GOTELEMETRY="off" \
    STNOUPGRADE=1 \
    YARN_ENABLE_TELEMETRY=false \
    NEXT_TELEMETRY_DISABLED=1 \
    GATSBY_TELEMETRY_DISABLED=1 \
    STORYBOOK_DISABLE_TELEMETRY=1 \
    DOTNET_CLI_TELEMETRY_OPTOUT=1 \
    DOTNET_EnableDiagnostics=0 \
    DOTNET_INTERACTIVE_CLI_TELEMETRY_OPTOUT=1 \
    MLDOTNET_CLI_TELEMETRY_OPTOUT="True" \
    DOTNET_SVCUTIL_TELEMETRY_OPTOUT=1 \
    CANVAS_LMS_STATS_COLLECTION="opt_out" \
    TELEMETRY_OPT_IN="" \
    ET_NO_TELEMETRY="ANY_VALUE" \
    WERF_TELEMETRY=0 \
    MSLAB_TELEMETRY_LEVEL="None" \
    ALLOW_UI_ANALYTICS="false" \
    NUKE_TELEMETRY_OPTOUT=1 \
    EARTHLY_DISABLE_ANALYTICS=1 \
    STRIPE_CLI_TELEMETRY_OPTOUT=1 \
    FEAST_TELEMETRY="False" \
    MELTANO_DISABLE_TRACKING="True" \
    QUILT_DISABLE_USAGE_METRICS="True" \
    CARBON_TELEMETRY_DISABLED=1 \
    FASTLANE_OPT_OUT_USAGE="YES" \
    GATSBY_TELEMETRY_DISABLED=1 \
    ORYX_DISABLE_TELEMETRY="true" \
    RASA_TELEMETRY_ENABLED="false" \
    CLOUDSDK_CORE_DISABLE_USAGE_REPORTING="true" \
    ALIBUILD_NO_ANALYTICS=1 \
    HOMEBREW_NO_ANALYTICS=1 \
    HOMEBREW_NO_ANALYTICS_THIS_RUN=1 \
    HOMEBREW_NO_AUTO_UPDATE=1 \
    DECK_ANALYTICS="off" \
    NG_CLI_ANALYTICS="false" \
    NG_CLI_ANALYTICS_SHARE="false" \
    INFLUXD_REPORTING_DISABLED="true" \
    MOBILE_CENTER_TELEMETRY="off" \
    ARDUINO_METRICS_ENABLED="false" \
    POWERSHELL_TELEMETRY_OPTOUT=1 \
    POWERSHELL_UPDATECHECK="Off" \
    LYNX_ANALYTICS=0 \
    DISABLE_QUICKWIT_TELEMETRY=1 \
    AUTOMAGICA_NO_TELEMETRY="1" \
    MM_LOGSETTINGS_ENABLEDIAGNOSTICS="false" \
    MM_SERVICESETTINGS_ENABLESECURITYFIXALERT="false" \
    APPCD_TELEMETRY=0 \
    BF_CLI_TELEMETRY="false" \
    DA_TEST_DISABLE_TELEMETRY=1 \
    CHOOSENIM_NO_ANALYTICS=1 \
    COCOAPODS_DISABLE_STATS="true" \
    CUBEJS_TELEMETRY="false" \
    HOOKDECK_CLI_TELEMETRY_OPTOUT=1 \
    VUEDX_TELEMETRY="off" \
    HINT_TELEMETRY="off" \
    BATECT_ENABLE_TELEMETRY="false" \
    DAGSTER_DISABLE_TELEMETRY=1 \
    TELEMETRY_DISABLED=1 \
    PROSE_TELEMETRY_OPTOUT=1 \
    SKU_TELEMETRY="false" \
    PANTS_ANONYMOUS_TELEMETRY_ENABLED="false" \
    CHECKPOINT_DISABLE=1 \
    ARM_DISABLE_TERRAFORM_PARTNER_ID="true" \
    SCOUT_DISABLE=1 \
    PULUMI_SKIP_UPDATE_CHECK="true" \
    DISABLE_CRASH_REPORT=1 \
    KICS_COLLECT_TELEMETRY=0 \
    INFRACOST_SELF_HOSTED_TELEMETRY="false" \
    INFRACOST_SKIP_UPDATE_CHECK="true" \
    DISABLE_AUTO_UPDATE="true" \
    BUGGER_OFF=1 \
    HASURA_GRAPHQL_ENABLE_TELEMETRY="false" \
    MEILI_NO_ANALYTICS="true" \
    NC_DISABLE_TELE=1 \
    ONE_CODEX_NO_TELEMETRY="True" \
    SQA_OPT_OUT="true" \
    ROCKSET_CLI_TELEMETRY_OPTOUT=1 \
    TUIST_STATS_OPT_OUT=1 \
    AUTOMATEDLAB_TELEMETRY_OPTIN=0 \
    AUTOMATEDLAB_TELEMETRY_OPTOUT=1 \
    CHEF_TELEMETRY_OPT_OUT=1 \
    DASH_DISABLE_TELEMETRY=1 \
    TYPO3_DISABLE_CORE_UPDATER=1 \
    REDIRECT_TYPO3_DISABLE_CORE_UPDATER=1 \
    IG_PRO_OPT_OUT="YES" \
    PNPPOWERSHELL_DISABLETELEMETRY="true" \
    PNPPOWERSHELL_UPDATECHECK="false" \
    SF_DISABLE_TELEMETRY="true" \
    APOLLO_TELEMETRY_DISABLED=1 \
    REPORTPORTAL_CLIENT_JS_NO_ANALYTICS="true" \
    SALTO_TELEMETRY_DISABLE=1 \
    VSTEST_TELEMETRY_OPTEDIN=0 \
    NUXT_TELEMETRY_DISABLED=1 \
    MSSQL_CLI_TELEMETRY_OPTOUT="True" \
    VAGRANT_CHECKPOINT_DISABLE=1 \
    VAGRANT_BOX_UPDATE_CHECK_DISABLE=1 \
    SFDX_DISABLE_TELEMETRY="true" \
    RESTLER_TELEMETRY_OPTOUT=1 \
    TEEM_DISABLE="true" \
    F5_ALLOW_TELEMETRY="false" \
    REACT_APP_WEBINY_TELEMETRY="false" \
    STRAPI_TELEMETRY_DISABLED="true" \
    STRAPI_DISABLE_UPDATE_NOTIFICATION="true" \
    SLS_TRACKING_DISABLED=1 \
    SLS_TELEMETRY_DISABLED=1 \
    SUGGESTIONS_OPT_OUT=1
