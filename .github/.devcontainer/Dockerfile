FROM mcr.microsoft.com/devcontainers/rust:bookworm

COPY --from=ghcr.io/astral-sh/uv:0.9.8 /uv /uvx /bin/

ENV PATH=$PATH:/opt/riscv/bin

COPY --from=ghcr.io/object-object/hexxy64-riscv-gnu-toolchain:rv64i-lp64 /opt/riscv /opt/riscv

COPY --from=ghcr.io/object-object/hexxy64-riscv-gnu-toolchain:rv64i-lp64-linux /opt/riscv /opt/riscv

# https://code.visualstudio.com/remote/advancedcontainers/persist-bash-history
ARG USERNAME=vscode

RUN SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
    && mkdir /commandhistory \
    && touch /commandhistory/.bash_history \
    && chown -R $USERNAME /commandhistory \
    && echo "$SNIPPET" >> "/home/$USERNAME/.bashrc" \
    && sed -i '/^__bash_prompt$/d' "/home/$USERNAME/.bashrc"
# ^ fix weird issues with shell not handling line wrapping

# buildroot dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends cpio bc device-tree-compiler \
    && rm -rf /var/lib/apt/lists/*

# avoid permission issues with cargo if cargo install runs as root
USER ${USERNAME}

RUN rustup toolchain install nightly-2025-06-10

RUN rustup target add riscv64i-unknown-none-elf

RUN rustup component add --toolchain nightly-2025-06-10 clippy llvm-tools

RUN cargo install cargo-binutils
