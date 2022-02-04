Instructions to generate HTML schedule and contacts from CSV file:

## Load NeoCsv and Mustache
```smalltalk
Metacello new
	repository: 'github://svenvc/NeoCSV/repository';
	baseline: 'NeoCSV';
	load.
Metacello new
	repository: 'github://noha/mustache/repository';
	baseline: 'Mustache';
	load.
```

## Generate HTML schedules
We first read the CSV file and then generate HTML chunks we aggregate into an ouput file.
```smalltalk
pharodaysRepo := FileLocator home / 'dev' / 'rmod' / 'pharodays'.
schedules := pharodaysRepo / 'templates' / 'schedule.csv' 
	readStreamDo: [ :readStream |
		(NeoCSVReader on: readStream)
			separator: $;;
			skipHeader;
			emptyFieldValue: '';
			addField;
			addField;
			addField;
			addField;
			upToEnd ].
		
scheduleDetailTemplate := (pharodaysRepo / 'templates' / 'schedule-detail.html') asFileReference contents asMustacheTemplate.

fullSchedule := ''.
schedules do: [ :schedule | | html |
	html := scheduleDetailTemplate value: { 
		'TIME' -> schedule first trim. 
		'SPEAKER' -> schedule second trim.
		'SUBJECT' -> schedule third trim.
		'ROOM' -> schedule fourth trim} asDictionary.
	fullSchedule := fullSchedule, html. ].
(pharodaysRepo / 'templates' / 'shedule-pharodays.html')
	ensureDelete;
	writeStreamDo: [ :s | s nextPutAll: fullSchedule ].
```
Then, you just neeed to copy/paste the generated HTML into the right place (index.html or workshop.html

## Generate HTML speakers
We first read the CSV file and then generate HTML chunks we aggregate into an ouput file.
```smalltalk

pharodaysRepo := FileLocator home / 'dev' / 'rmod' / 'pharodays'.
speakerTemplate := (pharodaysRepo / 'templates' / 'speaker.html') asFileReference contents asMustacheTemplate.

speakers := pharodaysRepo / 'templates' / 'speakers.csv' 
	readStreamDo: [ :readStream |
		(NeoCSVReader on: readStream)
			separator: $;;
			skipHeader;
			emptyFieldValue: '';
			addField;
			addField;
			addField;
			upToEnd ].

speakersHtml := ''.
speakers do: [ :speaker | | html |
	html := speakerTemplate value: { 
		'NAME' -> speaker first trim. 
		'IMAGE_PATH' -> speaker second trim.
		'JOB' -> speaker third trim} asDictionary.
	speakersHtml := speakersHtml, html. ].
(pharodaysRepo / 'templates' / 'speakers-pharodays.html')
	ensureDelete;
	writeStreamDo: [ :s | s nextPutAll: speakersHtml ].
```
Then, you just neeed to copy/paste the generated HTML into the right place (index.html)
