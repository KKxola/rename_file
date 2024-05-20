Organizando e Renomeando Arquivos de Imagem com Python
Este projeto é um script Python para renomear e organizar arquivos de imagem em um diretório com base na data de criação das imagens. As imagens são movidas para subdiretórios estruturados por ano, mês e dia, o que facilita a navegação e a gestão dos arquivos.

Funcionalidades
Renomear Arquivos: Renomeia os arquivos de imagem com base na data de criação no formato YYYY-MM-DD_HH-MM-SS.
Organização por Data: Move os arquivos renomeados para subdiretórios estruturados como YYYY/MM/DD.
Paralelismo: Utiliza múltiplos threads para aumentar a eficiência do processo.
Pré-requisitos
Python 3.x
Biblioteca Pillow (Python Imaging Library)
Instalação
Instale o Python: Certifique-se de ter o Python instalado. Você pode baixá-lo do site oficial do Python.

Instale as Bibliotecas Necessárias: Execute o seguinte comando para instalar a biblioteca Pillow:

bash
Copy code
pip install pillow
Uso
Clone o Repositório:

bash
Copy code
git clone https://github.com/seu-usuario/organize-images.git
cd organize-images
Configure o Diretório:

Edite o arquivo organize_images.py e altere o caminho do diretório em directories_to_process para o diretório que você deseja processar.
Execute o Script:

Abra um terminal na pasta do projeto e execute:
bash
Copy code
python organize_images.py
Código Principal
python
Copy code
import os
from datetime import datetime
from PIL import Image
from PIL.ExifTags import TAGS
from concurrent.futures import ThreadPoolExecutor

def get_creation_date(file_path):
    try:
        image = Image.open(file_path)
        exif_data = image._getexif()
        if exif_data is not None:
            for tag, value in exif_data.items():
                tag_name = TAGS.get(tag, tag)
                if tag_name == 'DateTimeOriginal':
                    return datetime.strptime(value, '%Y:%m:%d %H:%M:%S')
    except Exception as e:
        print(f"Error retrieving EXIF data for {file_path}: {e}")

    return datetime.fromtimestamp(os.path.getmtime(file_path))

def rename_and_move_file(file_path, base_directory):
    try:
        creation_date = get_creation_date(file_path)
        formatted_date = creation_date.strftime('%Y-%m-%d_%H-%M-%S')
        year = creation_date.strftime('%Y')
        month = creation_date.strftime('%m')
        day = creation_date.strftime('%d')
        
        file_extension = os.path.splitext(file_path)[1]
        new_file_name = f"{formatted_date}{file_extension}"
        
        new_directory = os.path.join(base_directory, year, month, day)
        os.makedirs(new_directory, exist_ok=True)
        
        new_file_path = os.path.join(new_directory, new_file_name)

        # Check if the new file name already exists and make it unique if necessary
        counter = 1
        while os.path.exists(new_file_path):
            new_file_name = f"{formatted_date}_{counter}{file_extension}"
            new_file_path = os.path.join(new_directory, new_file_name)
            counter += 1

        if file_path != new_file_path:
            os.rename(file_path, new_file_path)
            print(f"Renamed and moved {file_path} to {new_file_path}")
    except Exception as e:
        print(f"Error processing {file_path}: {e}")

def rename_and_organize_files_in_directory(directory):
    files_to_rename = []
    for root, dirs, files in os.walk(directory):
        for file_name in files:
            file_path = os.path.join(root, file_name)
            files_to_rename.append(file_path)

    with ThreadPoolExecutor(max_workers=6) as executor:
        executor.map(lambda file_path: rename_and_move_file(file_path, directory), files_to_rename)

if __name__ == '__main__':
    directories_to_process = [
        r"C:\Users\kaued\OneDrive\Imagens"
    ]

    for directory in directories_to_process:
        rename_and_organize_files_in_directory(directory)

    print("Renomeação e organização concluídas!")
Contribuição
Se você encontrar algum problema ou tiver sugestões de melhorias, sinta-se à vontade para abrir uma issue ou enviar um pull request.

Licença
Este projeto está licenciado sob a licença MIT. Veja o arquivo LICENSE para mais detalhes.

Estrutura do Repositório
Copy code
organize-images/
│
├── LICENSE
├── README.md
└── organize_images.py
Observações
Backup: Faça backup dos seus arquivos antes de executar o script para evitar perda de dados.
Permissões: Certifique-se de ter permissões adequadas para ler e escrever no diretório especificado.
