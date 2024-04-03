Nee makaun iha Tetum ba `README.md` atu hatudu kona-ba sira hotu hotu atu kria funsionalidade filtru ba dadus baze liu husi opsaun ba tinan no fulan iha Laravel:

```markdown
# Laravel Filtru Dadus Tuir Tinan no Fulan

Nee mak guia ki'ik liu kona-ba kona-kona atu kria funsionalidade filtru ba dadus baze liu husi tinan no fulan iha aplikasaun Laravel.

## Rekizitus Previu

- PHP >= 8.0
- Composer
- Servidor Web (Apache, Nginx)
- Database (MySQL, PostgreSQL, SQLite, SQL Server)

## Instalasaun

### Passu 1: Instala Laravel
Se ita seidauk instala Laravel, ita bele instala liu husi Composer ho komandu ne'e:

```bash
composer global require laravel/installer
```

Kria projeitu Laravel foun:

```bash
laravel new nama-proyek
```
ka

```bash
composer create-project --prefer-dist laravel/laravel nama-proyek
```

### Passu 2: Kria Migrasaun ba Database
Kria ficheiru migrasaun atu define estrutura tabela:

```bash
php artisan make:migration create_mahasiswas_table
```

Define estrutura tabela iha ficheiru migrasaun (`database/migrations/xxxx_xx_xx_xxxxxx_create_mahasiswas_table.php`):

```php
public function up()
{
    Schema::create('mahasiswas', function (Blueprint $table) {
        $table->id();
        $table->string('nama_mahasiswa');
        $table->string('alamat');
        $table->string('departament');
        $table->date('tanggal_rejist');
        $table->timestamps();
    });
}
```

Run migrasaun atu kria tabela:

```bash
php artisan migrate
```

### Passu 3: Kria Modelu
Kria modelu `Mahasiswa`:

```bash
php artisan make:model Mahasiswa
```

Define modelu `Mahasiswa` iha ficheiru `app/Models/Mahasiswa.php`:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Mahasiswa extends Model
{
    use HasFactory;

    protected $fillable = ['nama_mahasiswa', 'alamat', 'departament', 'tanggal_rejist'];
}
```

### Passu 4: Kria Kontrolador
Kria kontrolador `MahasiswaController`:

```bash
php artisan make:controller MahasiswaController
```

Define método index atu hatudu no filtra dadus iha ficheiru `app/Http/Controllers/MahasiswaController.php`:

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Mahasiswa;

class MahasiswaController extends Controller
{
    public function index(Request $request)
    {
        $tahun = $request->get('tahun');
        $bulan = $request->get('bulan');

        $mahasiswas = Mahasiswa::query();

        if ($tahun) {
            $mahasiswas->whereYear('tanggal_rejist', $tahun);
        }

        if ($bulan) {
            $mahasiswas->whereMonth('tanggal_rejist', $bulan);
        }

        $mahasiswas = $mahasiswas->get();

        return view('mahasiswa.index', compact('mahasiswas'));
    }
}
```

### Passu 5: Kria routes
Adiciona rota ba `MahasiswaController` iha ficheiru `routes/web.php`:

```php
use App\Http\Controllers\MahasiswaController;

Route::get('/mahasiswa', [MahasiswaController::class, 'index'])->name('mahasiswa.index');
```

### Passu 6: Kria Vista
Kria ficheiru vista `index.blade.php` iha pasta `resources/views/mahasiswa/` atu hatudu dadus no formuláriu filtru:

```php
<!-- Formuláriu -->
<form action="{{ route('mahasiswa.index') }}" method="GET">
    <!-- Dropdown ba Tinan -->
    <div class="mb-3">
        <label for="tahun" class="form-label">Tinan</label>
        <select name="tahun" id="tahun" class="form-select">
            <option value="">Pili Tinan</option>
            @for ($i = now()->year; $i >= 2000; $i--)
                <option value="{{ $i }}" {{ request('tahun') == $i ? 'selected' : '' }}>{{ $i }}</option>
            @endfor
        </select>
    </div>

    <!-- Dropdown ba Fulan -->
    <div class="mb-3">
        <label for="bulan" class="form-label">Fulan</label>
        <select name="bulan" id="bulan" class="form-select">
            <option value="">Pili Fulan</option>
            @foreach (range(1, 12) as $bulan)
                <option value="{{ $bulan }}" {{ request('bulan') == $bulan ? 'selected' : '' }}>{{ date('F', mktime(0, 0, 0, $bulan, 1)) }}</option>
            @endforeach
        </select>
    </div>

    <!-- Botão ba Haree -->
    <button type="submit" class="btn btn-primary">Filtru</button>
</form>

<!-- Tabela Dadus -->
<table class="table">
    <thead>
        <tr>
            <th>ID Mahasiswa</th>
            <th>Naran Mahasiswa</th>
            <th>Endereçu</th>
            <th>Departamentu</th>
            <th{Tinan rejistru</th>
        </tr>
    </thead>
    <tbody>
        @foreach ($mahasiswas as $mahasiswa)
            <tr>
                <td>{{ $mahasiswa->id }}</td>
                <td>{{ $mahasiswa->nama_mahasiswa }}</td>
                <td>{{ $mahasiswa->alamat }}</td>
                <td>{{ $mahasiswa->departament }}</td>
                <td>{{ $mahasiswa->tanggal_rejist }}</td>
            </tr>
        @endforeach
    </tbody>
</table>
```

## Konkluzon
Ho passu sira ne'e, ita kria funsionalidade filtru ba dadus baze liu husi tinan no fulan ba tabela `mahasiswas`. Ita bele asesu pájina ne'e iha `http://localhost:8000/mahasiswa`.

Kódigu di'ak!
```

Ita bele sae kontinu iha `README.md` iha diretóriu raiz ba ita-nia projeitu Laravel.