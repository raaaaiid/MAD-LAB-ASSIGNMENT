# MAD-LAB-ASSIGNMENT
import 'dart:math';

class Citizen {
    late String _name;
    late DateTime _dob;
    late dynamic _gender;
    late Set<String> _districts;
    late Map<String, String> _districtCodeMap;
    late String _cnic;
    late int _age;

    Citizen.newborn(this._name, this._dob, this._gender, this._districts,
                    this._districtCodeMap) {
        _age = 0;
        _generateCNIC();
    }

    Citizen(this._name, this._dob, this._gender, this._districts,
            this._districtCodeMap) {
        _age = DateTime.now().year - _dob.year;
        _generateCNIC();
    }

    String get name => _name;

    set name(String value) {
        _name = value;
    }

    DateTime get dob => _dob;

    set dob(DateTime value) {
        _dob = value;
    }

    dynamic get gender => _gender;

    set gender(dynamic value) {
        _gender = value;
    }

    Set<String> get districts => _districts;

    set districts(Set<String> value) {
        _districts = value;
    }

    Map<String, String> get districtCodeMap => _districtCodeMap;

    set districtCodeMap(Map<String, String> value) {
        _districtCodeMap = value;
    }

    String get cnic => _cnic;

    int get age => _age;

    void _generateCNIC() {
        var random = Random();
        var cnic = '';
        cnic += _dob.year.toString().substring(2);
        cnic += _dob.month.toString().padLeft(2, '0');
        cnic += _dob.day.toString().padLeft(2, '0');
        cnic += _districtCodeMap[_districts.first]!;
        cnic += random.nextInt(10).toString();
        cnic += random.nextInt(10).toString();
        cnic += random.nextInt(10).toString();
        cnic += random.nextInt(10).toString();
        cnic += random.nextInt(10).toString();
        _cnic = cnic;
    }
}

class Passport extends Citizen {
    late String _passportType;
    late DateTime _issueDate;
    late DateTime _expiryDate;

    Passport.newborn(String name, DateTime dob, dynamic gender, Set<String> districts,
                     Map<String, String> districtCodeMap, this._passportType)
    : super.newborn(name, dob, gender, districts, districtCodeMap) {
        _issueDate = DateTime.now();
        _expiryDate = _issueDate.add(Duration(days: 1825));
    }

    Passport(String name, DateTime dob, dynamic gender, Set<String> districts,
             Map<String, String> districtCodeMap, this._passportType)
    : super(name, dob, gender, districts, districtCodeMap) {
        _issueDate = DateTime.now();
        _expiryDate = _issueDate.add(Duration(days: 1825));
    }

    String get passportType => _passportType;

    set passportType(String value) {
        _passportType = value;
    }

    DateTime get issueDate => _issueDate;

    set issueDate(DateTime value) {
        _issueDate = value;
    }

    DateTime get expiryDate => _expiryDate;

    bool get isExpired => DateTime.now().isAfter(_expiryDate);

    factory Passport.fromCitizen(Citizen citizen, String passportType) {
        return Passport(
                   citizen.name,
                   citizen.dob,
                   citizen.gender,
                   citizen.districts,
                   citizen.districtCodeMap,
                   passportType);
    }

    factory Passport.fromPassport(Passport passport,
    {String? passportType, DateTime? issueDate}) {
        return Passport(
                   passport.name,
                   passport.dob,
                   passport.gender,
                   passport.districts,
                   passport.districtCodeMap,
                   passportType ?? passport.passportType)
               .._issueDate = issueDate ?? passport.issueDate;
    }
}

class Family {
    late List<Citizen> _members;

    Family(List<Citizen> members) {
        _members = members;
    }

    List<Citizen> get members => _members;

    void addMember(Citizen member) {
        _members.add(member);
    }

    void removeMember(Citizen member) {
        _members.remove(member);
    }
}

void main() {
    // create some citizens
    Citizen father =
        Citizen('Asif Azhar', DateTime(1972, 4, 5), 'Male', {'Lahore'}, {'Lahore': 'LHR'});
    Citizen mother =
        Citizen('Hina Asif', DateTime(1972, 5, 12), 'Female', {'Lahore'}, {'Lahore': 'LHR'});
    Citizen son =
        Citizen('Raid Asif', DateTime(2002, 3, 1), 'Male', {'Lahore'}, {'Lahore': 'LHR'});

    // create passports for some citizens
    Passport fatherPassport = Passport.fromCitizen(father, 'P');
    Passport sonPassport = Passport.fromCitizen(son, 'P');

    // create a family
    Family family = Family([father, mother, son,fatherPassport,sonPassport]);

    // print details of each family member
    for (var member in family.members) {
        print('Name: ${member.name}');
        print('Date of Birth: ${member.dob}');
        print('Gender: ${member.gender}');
        print('CNIC: ${member.cnic}');
        if (member is Passport) {
            print('Passport Type: ${member.passportType}');
            print('Passport Issue Date: ${member.issueDate}');
            print('Passport Expiry Date: ${member.expiryDate}');
        } else {
            print('CNIC Districts: ${member.districts}');
            print('CNIC District Codes: ${member.districtCodeMap}');
        }
        print('Age: ${member.age}');
    }
}
