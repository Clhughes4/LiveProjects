# Python Live Project

## Introduction
At the end of the python course with the Tech Academy, I participated in a 2 week long live project creating a Django MVT web app for hobbyists to keep track of their collection of items, utilizing an api and saving the data to a database. I worked with my peers on a team to create an app within an established template Hobby Manager to work from. Daily I participated in stand up meetings which consisted of discussing what work was accomplished the previous day, current issues being faced, and what was going to be my focus to tackle for the day. Each of us chose which hobby to focus on from art to recipes to video games...etc, to my focus which was all centered around Game of Thrones. Each task or assignment was sectioned off into a "user story", which I participated in 8 of them. Each story presented a new level of difficulty and a new concept to grasp through the use of research and application. Below you will find some of my contributions to the project including some of the stories I worked on as well as some code snippets


## Template
- Created the table for all the data to be presented to the user for easy readability

```html
<section>
    <div class="flex-container" id="characterCollectionPage">
        <table class="table table-dark rounded">
            <tr class="table-bordered">
                <th scope="col">Name</th>
                <th scope="col">Alias</th>
                <th scope="col">Actor</th>
                <th scope="col">House</th>
                <th scope="col">Sigil</th>
                <th scope="col">Kingdom</th>
            </tr>
            {% for character in characters %}       
            <tr class="table-sm table-striped">
                <td scope="col">{{character.name}}</td>
                <td scope="col">{{character.alias}}</td>
                <td scope="col">{{character.actor}}</td>
                <td scope="col">{{character.house}}</td>
                <td scope="col">{{character.sigil}}</td>
                <td scope="col">{{character.kingdom}}</td>
                <td scope="col"><a href="{{character.pk}}/Details"><button class="primary-light-button">Details</button></a></td>    
            </tr>
            {% endfor %}
        </table>
        <button class="primary-bright-button" type="button" onclick=" location.href='{% url 'createCharacter' %}'">Add to Collection</button>
    </div>
</section>

{% endblock %}
```
## Model
- Model used to create the database

```python
CHARACTER_OPTIONS = (('Direwolf', 'Direwolf'), ('Lion', 'Lion'), ('Three-Headed Dragon', 'Three-Headed Dragon'), ('Stag', 'Stag'), ('Trout', 'Trout'),
                     ('Rose', 'Rose'), ('Falcon', 'Falcon'), ('Kraken', 'Kraken'), ('Sun & Spear', 'Sun & Spear'))

class Character(models.Model):
    name = models.CharField(max_length=60,default="", blank=True)
    alias = models.CharField(max_length=75, default="", blank=True)
    actor = models.CharField(max_length=75,default="",blank=True)
    house = models.CharField(max_length=70,default="",blank=True)
    sigil = models.CharField(max_length=70, default="", blank=True, choices=CHARACTER_OPTIONS)
    kingdom = models.CharField(max_length=70, default="", blank=True)


    Characters = models.Manager()

    def __str__(self):
        return self.name
```
## View
- views.py

```python
def home(request):                                     
    return render(request, 'GotDemo/Got_home.html')

# Function which controls the index page for the Got App
def index(request):
    get_characters = Character.Characters.all()         
    context = {'characters': get_characters}            
    return render(request, 'GotDemo/Got_index.html',context)

# Function to add a new character to the database
def add_character(request):
    form = CharacterForm(request.POST or None)     
    if form.is_valid():                         
        form.save()                             
        return redirect('showCharacters')       
    else:
        print(form.errors)                      
        form = CharacterForm()                  
    return render(request, 'GotDemo/Got_create.html', {'form':form})

# Function to check the details of one particular character in the database
def details_character(request, pk):                     
    pk = int(pk)                                       
    character = get_object_or_404(Character, pk=pk)     
    context = {'character':character}                   
    return render(request, 'GotDemo/Got_details.html', context)     

#Function to edit the details of a character
def edit_character(request, pk):
    pk = int(pk)
    character = Character.Characters.get(pk=pk)         
    form = CharacterForm(data=request.POST or None, instance=character)         
    if request.method == 'POST':                                                
        if form.is_valid():                                                     
            form.save()                                                         
            return redirect('characterDetails',pk)                              
        else:                                                                   
            print(form.errors)                                                   
    else:
        return render(request,'GotDemo/Got_edit.html', {'form':form})       

# Function to delete character
def delete_character(request, pk):
    pk = int(pk)
    character = get_object_or_404(Character, pk=pk)
    context = {'character': character}                                 
    if request.method == 'POST':
        character.delete()                                              
        return redirect('showCharacters')                               
    else:
        return render(request, 'GotDemo/Got_delete.html', context)


def api_response(request):                  
    context = {'world': []}
    if request.method == 'POST':            
        print(request.POST)
        if 'character' in request.POST:     
            character_id = request.POST['character']        
            response = get_character(character_id)          
            context.update({'character':response,})
            print(response)                                 

        elif 'house' in request.POST:
            house_id = request.POST['house']
            response = get_house(house_id)
            context.update({'house':response,})
            print(response)

        elif 'region' in request.POST:
            region_id = request.POST['region']
            response = get_region(region_id)
            context.update({'region':response,})
            for item in response:
                house = item['name']
                context['world'].append(house)
    return render(request, 'GotDemo/Got_api.html', context)

```

## Key Takeaways
- All members of the team contribute to the overall success of the project as long as the drive and goals are parallel
- Each story presented new and different class of obstacles to overcome which helped to hone and sharpen my skills further along with developing new ones
- When using an API it is absolutely crucial to understand all the documentation and the proper parameters needed for true functionality
