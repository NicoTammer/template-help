# template-help

Heb je een foutje gezien? Fix het een maak een pull request! :D

## Benodigdheden die je misschien niet al hebt :)
- Visual Studio 2022  https://visualstudio.microsoft.com/vs/
- PostgreSQL          https://www.postgresql.org/download/
- Postman             https://www.postman.com/downloads/
- Dotnet SDK          https://dotnet.microsoft.com/en-us/download

## Set up werk!
Nu je (hoop ik) alles hebt kunnen we beginnen!
1. Navigeer naar `{naam-van-je-project/src/presentation}`
2. Kopieer en plak `appsettings.json` zodat je er twee hebt
3. Verander de naam van je kopie naar `appsettings.Development.json`
4. Open `appsettings.Development.json` in iets als Notepad++, notepad of vs code
   - Er zijn twee variabelen die we hier aan moeten passen! De SecurityKey en de DefaultConnection.
   - SecurityKey welke we moeten vervangen met een 64 karakter lange random string. Hiermee worden je wachtwoorden gehashed, tokens gevalideert etc. Heeeel belangrijk dat je dit soort dingen niet meecommit in git. Vandaar dat we het niet in appsettings zetten maar in een kopie waar git niet naar kijkt. <img width="768" alt="SecurityKey" src="https://user-images.githubusercontent.com/7372864/191063122-7acf520a-17a3-4047-9c98-0aee2c22c4bf.png">
    - DefaultConnection waarmee we de API vertellen hoe het bij de database uitkomt. Uiteraard met je eigen username/password en gewenste databasename. De database hoeft niet te bestaan! Deze maakt de app zelf aan zolang je postgres installatie goed gegaan is en je de juiste username/password combinatie hebt ingevuld. <img width="991" alt="DefaultConnection" src="https://user-images.githubusercontent.com/7372864/191063897-07a641e9-902a-4780-a234-e86edc5c9593.png">
    
## Opstarten
1. Navigeer naar de root folder van je project. 
2. Dubbelklik op het bestand die eindigt op .sln. Het project opent dan in Visual Studio
3. Open in de solution explorer (gebruikelijk rechts in je scherm) de src map zodat je de onderliggende projecten ziet![afbeelding](https://user-images.githubusercontent.com/7372864/191064676-6064dbaf-d7a7-43b8-bc1d-cc499947e094.png)
4. Rechtsklik op Presentation en selecteer ![afbeelding](https://user-images.githubusercontent.com/7372864/191064770-30afec59-39a4-49eb-a196-fb17079cad7d.png)
5. Aan de bovenkant van je scherm, klik op het kleine pijltje naast de play knop met text IIS Express en kies voor Presentation. ![afbeelding](https://user-images.githubusercontent.com/7372864/191065164-8699801e-de09-4c71-9de0-f1f181a6fc56.png)
6. Druk nu op de ![afbeelding](https://user-images.githubusercontent.com/7372864/191065666-d2e55945-b147-4a15-bf70-e9fb23f1ad1a.png)
 knop. Je API runt nu als het goed is!

## Nieuwe entiteit maken
1. Stop je API als hij nog draait, shift+f5 of rode stopknop boven in je scherm.
2. Klap het Domain project uit, rechtsklik op entities -> add -> new class -> geef 'm een naam, die van mij heet Dog
3. Als het een entiteit is waar je wil dat er een Id, creatiedatum, persoon die 'm gemaakt heeft, voor het laatst aangepast heeft en wanner kan je je entiteit uitbreiden met de `BaseAuditableEntity`: ![afbeelding](https://user-images.githubusercontent.com/7372864/191070673-83f4747d-1b74-4b89-9230-ac485a97908a.png)
4. Als je alleen een Id wil kan je je entity uitbreiden met `BaseEntity`
5. Geef nu een property of twee aan je entiteit. Mijn hond krijgt een naam. Vergeet hierbij niet je getters en setters. Je hoeft hier niet zoals bij java aparte methodes voor te maken, ` { get; set; } is voldoende. Voor nu tot ik geloof de nieuwe versie van dotnet of c# moeten we achter nullable types ook = default! zetten anders blijft er een lelijke warning onder je property staan. :)![afbeelding](https://user-images.githubusercontent.com/7372864/191070442-40111703-e301-4580-a247-6efbcf5ed01b.png)

## Vertellen aan de Entity Framework dat er een nieuw entiteit is
1. Klap het Application project uit, vervolgens common en dan interfaces en open `IApplicationDbContext.cs`
2. Hier zie je een aantal DbSets van verschillende types. Hier willen we ons entiteit aan toevoegen! 
3. Voor mij is dat `DbSet<Dog> Dogs { get; }` en ik vind het fijn om ze op alfabetische volgorde te houden. Deze db sets vertaalt entity framework voor ons naar database tabellen!
4. Als je nu je project build door middel van `ctrl+shift+b` of in het build menu boven in je scherm komt er als het goed is een error naar voren! Dit omdat de hond wel aan de interface is toegevoegd in het application project maar de interface geimplementeert wordt in het infrastructure project! Klik op de error en het bestand opent.
5. Ook hier voegen we ons entiteit toe, iets anders dan in de interface: `public DbSet<Dog> Dogs => Set<Dog>();`
6. Build nog een keer en verifieer dat alles goed gaat, er moeten nu geen errors meer zijn.

## Entity Framework een tabel laten maken van het nieuwe entiteit door middel van Migrations
1. Klik boven in je scherm op tools -> NuGet Package Manager -> Package Manager Console
2. Onderin je scherm staat nu een tabje `Package Manager Console`
3. Zorg dat hier het default project op Infrastructure staat ![afbeelding](https://user-images.githubusercontent.com/7372864/191072626-529e4d3c-c5af-4a43-96a5-6c5bcbc68cd8.png)
4. Type in de package manager console: `add-migration BeschrijvingVanWatJeVerandertHebt` voor mij is het `add-migration AddDogs`
5. Hij doet even builden, en dan opent als het goed gaat de migration. Hier zou je nog dingen kunnen veranderen, default waardes etc maar voor nu niet aanzitten en gewoon laten zoals 't is
6. Type in de package manager console: `update-database`. Entity Framework voert dan de laatste migratie uit. 

## Command en Query maken (Minder in depth want veel code)
1. Rechtsklik weer op het Application project -> Add -> New Folder en geef deze de naam van je entiteit in *meervoud*
2. Maak twee mappen aan in je in stap 1 gemaakte map genaamd Commands en Queries
3. Maak een (in mijn geval) GetDogs map aan in de Queries map.
4. Maak een (in mijn geval) CreateDog map aan in de Commands map.
5. Voeg de volgende classes (met jouw entiteit naam) toe:
![afbeelding](https://user-images.githubusercontent.com/7372864/205299829-01dab1c5-2156-4651-b219-84cce3d09d8c.png)
6. We gaan eerst de Query maken. Open DogDto (Data Transfer Object) in Queries/GetDogs. Hier geven we aan wat we terug willen geven van een Dog aan de user. Ik wil dat de user alleen de Id en de Name van de dog kan zien dus dit is mijn Dto, pas aan naar jouw entiteit:
```dotnet 
using Template.Application.Common.Mappings;
using Template.Domain.Entities;

namespace Template.Application.Dogs.Queries.GetDogs;

public class DogDto : IMapFrom<Dog>
{
	public DogDto()
	{
	}

	public int Id { get; set; }

	public string? Name { get; set; }
}
```
7. Open DogsVm (Viewmodel) in Queries/GetDogs. Hier geven we de gebruiker de lijst van dogs. Pak mijn code en pas aan:
```
namespace Template.Application.Dogs.Queries.GetDogs;

public class DogsVm
{
	public IList<DogDto> Dogs { get; set; } = new List<DogDto>();
}

```
8. Open GetDogsQuery Ook hier geef ik je mijn code maar geef ik wat meer context :)
```
using AutoMapper;
using AutoMapper.QueryableExtensions;
using MediatR;
using Microsoft.EntityFrameworkCore;
using Template.Application.Common.Interfaces;
using Template.Application.Common.Security;
using Template.Domain.Enums;

namespace Template.Application.Dogs.Queries.GetDogs;

public record GetDogsQuery : IRequest<DogsVm>;

public class GetDogsQueryHandler : IRequestHandler<GetDogsQuery, DogsVm>
{
	// Dit is waar we onze DbSet Dogs hebben toegevoegd zodat EF weet dat er rekening mee gehouden moet worden
	private readonly IApplicationDbContext _context; 
	private readonly IMapper _mapper;

	// We roepen zelf deze class niet aan dus we geven deze waardes ook nooit mee aan de constructor
	// ze komen uit de Dependency Injection container. Zoek vooral op wat dat is, beetje complex maar ook simpel
	public GetDogsQueryHandler(IApplicationDbContext context, IMapper mapper)
	{
		_context = context;
		_mapper = mapper;
	}

	public async Task<DogsVm> Handle(GetDogsQuery request, CancellationToken cancellationToken)
	{
		return new DogsVm
		{
			// De context is onze database en heeft al onze tabellen. Het is een geabstraheerde query tool
			// Ook hier even naar googlen om te kijken wat mogelijk is, ook beetje complex maar ook simpel :)
			Dogs = await _context.Dogs
				.AsNoTracking()
				.ProjectTo<DogDto>(_mapper.ConfigurationProvider)
				.OrderBy(t => t.Name)
				.ToListAsync(cancellationToken)
		};
	}
}
```
9. Query klaar, on to the command! Open het CreateDogCommand en pas mijn code aan voor jouw entiteit:
```
using MediatR;
using Template.Application.Common.Interfaces;
using Template.Domain.Entities;

namespace Template.Application.Dogs.Commands.CreateDog;

public record CreateDogCommand : IRequest<int>
{
	public string? Name { get; init; }
}

public class CreateDogCommandValidator : AbstractValidator<CreateDogCommand>
{
	private readonly IApplicationDbContext _context;

	public CreateDogCommandValidator(IApplicationDbContext context)
	{
		_context = context;

		RuleFor(v => v.Name)
			.NotEmpty().WithMessage("Name is required.")
			.MaximumLength(200).WithMessage("Name must not exceed 200 characters.")
			.MustAsync(BeUniqueTitle).WithMessage("The specified name already exists.");
	}

	public async Task<bool> BeUniqueTitle(string name, CancellationToken cancellationToken)
	{
		return await _context.Dogs
			.AllAsync(l => l.Name != name, cancellationToken);
	}
}

public class CreateDogCommandHandler : IRequestHandler<CreateDogCommand, int>
{
	private readonly IApplicationDbContext _context;

	public CreateDogCommandHandler(IApplicationDbContext context)
	{
		_context = context;
	}

	public async Task<int> Handle(CreateDogCommand request, CancellationToken cancellationToken)
	{
		var entity = new Dog
		{
			Name = request.Name
		};

		_context.Dogs.Add(entity);

		await _context.SaveChangesAsync(cancellationToken);

		return entity.Id;
	}
}
```
10. Speel vooral met de validation, als je jouw entiteit meer properties geeft moet je wel weer op nieuw een migration maken en de database updaten zoals in de eerdere stappen. Het is vrij vanzelfsprekend, kijk vooral welke opties je hebt wanneer je RuleFor(v => v.Name). neerzet, dan opent er zo'n context menu dingetje met al je opties. En google!

## De nieuwe controller maken
We hebben nu ons command en query, hebben we weinig aan zonder een controller waar onze frontenders tegen kunnen praten. Voor het gemak maken we de routes toegankelijk zonder in te loggen, iedereen wil natuurlijk zijn hond delen met de wereld :)

1. Klap het Presentation project uit, rechtsklik op Controllers -> Add -> Controller, kies voor MVC Controller - Empty, druk op add, vul de naam van je gemaakte entiteit in *meervoud*+controllers in (`DogsController` voor mij) en klik weer op Add 
2. vervang vervolgens de de inhoud van het bestand met de onderstaande code en pas het aan naar jouw entiteit:
```
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Template.Application.Dogs.Commands.CreateDog;
using Template.Application.Dogs.Queries.GetDogs;

namespace Template.Presentation.Controllers;
public class DogsController : ApiControllerBase
{
	[AllowAnonymous]
	[HttpGet]
	public async Task<ActionResult<DogsVm>> Get()
	{
		return await Mediator.Send(new GetDogsQuery());
	}

	[AllowAnonymous]
	[HttpPost]
	public async Task<ActionResult<int>> Create(CreateDogCommand command)
	{
		return await Mediator.Send(command);
	}
}
```
3. Belangrijke dingen om hier te googlen zijn de HTTP Verbs (Get, Post, Patch, etc.) en hoe je die goed gebruik en het REST principe en hoe je dit goed toepast in een API.

## API Aanspreken met Postman
1. Open postman, maak een nieuw request
2. Voer als request URL in: http://localhost:5001/api/dogs (maar dan jouw entiteit)
3. Start je API op met het groene play knoppie in visual studio!
4. Terug naar postman, zorg dat de HTTP Methode op GET staat, druk op de blauwe Send knop (Misschien zegt je postman iets over SSL validation, dit kan je uitzetten in de settings of in dat bericht zelf door op disable ssl verification oid te klikken
5. Als het goed is krijg je een lege lijst.. maar wel een 200 ok! We moeten even een of meerdere dogs aanmaken.
6. Verander de http methode naar post
7. klik op body, raw en dan op text. Selecteer uit dat lijstje JSON
8. Vul volgende in bij de body, aangepast naar de properties die je op jouw CreateCommand hebt:
```
{
    "name": "Nico"
}
```
9. Nu kreeg je als het goed gegaan is een nummer terug. Als we uiteindelijk functionaliteit implementeren om specifieke Dogs op te halen kunnen we die gebruiken!
10. Voer stap 2 t/m 4 nog eens uit om onze dogs weer op te halen. Nu krijg je als het goed is wel een dog terug!
