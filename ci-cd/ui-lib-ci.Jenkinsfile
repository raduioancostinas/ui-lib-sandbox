@Library('osp-jenkins-shared-library@try/ui-lib') _

import com.masorange.osp.Config
import com.masorange.osp.PodTemplateBuilder
import com.masorange.osp.uilib.PlaywrightSidecar

// SMOKE TEMPORAL: valida sidecar Playwright (imagen, Chromium, /dev/shm, workspace). Revertir tras probar.
def PW_IMAGE = 'mcr.microsoft.com/playwright:v1.63.0-noble@sha256:eff16c30e6f3f4af0a03fa4b706120d5e9b0891c344a27d64559aff5900a4a27'

def cfg = Config.applyDefaults([mode: 'ci', playwrightImage: PW_IMAGE])
cfg.extraContainers = PlaywrightSidecar.containerYaml(PW_IMAGE)
cfg.extraVolumes = PlaywrightSidecar.volumesYaml()

podTemplate(PodTemplateBuilder.podTemplateArgs(cfg)) {
    node(POD_LABEL) {
        container('playwright') {
            sh '''
                set -e
                echo "node: $(node -v)"
                echo "pnpm presente (esperado vacio): $(command -v pnpm || true)"
                df -h /dev/shm
                ls -ld /home/jenkins/agent
                id
                CHROME=$(find /ms-playwright -type f \\( -name chrome -o -name chrome-headless-shell \\) | head -1)
                echo "chrome: $CHROME"
                "$CHROME" --headless=new --no-sandbox --disable-gpu --dump-dom about:blank
            '''
        }
    }
}
