# Mini-Portfolio
# atividade - 3a Escola de Programação Web para Meninas e Mulheres 2026

from pathlib import Path
import zipfile, shutil

base = Path("/mnt/data/mini_portfolio_jeinne")
html_path = base / "index.html"
img_path = base / "foto-jeinne.jpg"

assert html_path.exists(), "Não encontrei o index.html anterior."
assert img_path.exists(), "Não encontrei a foto na pasta do portfólio."

html_text = html_path.read_text(encoding="utf-8")

# Keep the image as a local file beside index.html and use a simple relative path.
html_text = html_text.replace(
    'src="foto-jeinne.jpg"',
    'src="./foto-jeinne.jpg"'
)

# Replace the previous YouTube embed with the newly supplied video.
start = html_text.find('            <iframe class="musica"')
end = html_text.find('            </iframe>', start)
if start != -1 and end != -1:
    end += len('            </iframe>')
    new_iframe = '''            <iframe class="musica"
                src="https://www.youtube-nocookie.com/embed/WVe80iZtlYU?autoplay=1"
                title="Música escolhida por Jeinne"
                allow="autoplay; encrypted-media; picture-in-picture"
                referrerpolicy="strict-origin-when-cross-origin"
                allowfullscreen>
            </iframe>'''
    html_text = html_text[:start] + new_iframe + html_text[end:]

# Add a direct YouTube link as a fallback if embedding is disabled by YouTube.
needle = '''            <p class="aviso">
                Observação: alguns navegadores e o próprio YouTube podem impedir'''
replacement = '''            <p>
                Se o vídeo não aparecer ou não tocar, <a href="https://www.youtube.com/watch?v=WVe80iZtlYU"
                target="_blank" rel="noopener noreferrer">assista à música diretamente no YouTube</a>.
            </p>
            <p class="aviso">
                Observação: alguns navegadores e o próprio YouTube podem impedir'''
html_text = html_text.replace(needle, replacement)

html_path.write_text(html_text, encoding="utf-8")

zip_path = Path("/mnt/data/mini_portfolio_jeinne_atualizado.zip")
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as z:
    for p in [html_path, img_path, base / "lista de livros.txt"]:
        if p.exists():
            z.write(p, arcname=f"mini_portfolio_jeinne/{p.name}")

print("Versão atualizada criada.")
print(f"HTML: {html_path} ({html_path.stat().st_size} bytes)")
print(f"Foto: {img_path} ({img_path.stat().st_size} bytes)")
print(f"ZIP: {zip_path} ({zip_path.stat().st_size} bytes)")
