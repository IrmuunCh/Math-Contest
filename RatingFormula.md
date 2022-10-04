```c++
#include<bits/stdc++.h>
using namespace std;
#define pb push_back
#define ff first
#define ss second
struct Student{
    string Name;
    int Rating;
    int Class;
    int ContestCount;
    int CalculateRating;
};
Student st[1000];
map<string,int>id;
double elo(int r1,int r2){
    return (double)1/(1+pow(10,(r1-r2)/(double)400));
}
struct Participant{
    int Place;
    string Name;
};
int main(){

    int additionalRating[7]={750,500,300,150,50,0};

    //File convert
    ifstream fin("Rating.txt");
    ofstream fout("RatingChange.txt");

    //Read Information
    int StudentCount;
    fin>>StudentCount;
    for(int i=1;i<=StudentCount;i++){
        st[i].Name=to_string(i);
        st[i].Class=0;
        st[i].ContestCount=0;
        fin>>st[i].Name>>st[i].Rating>>st[i].ContestCount;
        st[i].CalculateRating=st[i].Rating+additionalRating[min(st[i].ContestCount,5)];
        id[st[i].Name]=i;
    }

    fout<<"OK"<<endl;
    
    int ParticipantCount;
    fin>>ParticipantCount;
    Participant Participants[ParticipantCount];
    vector<pair<int,string>>PlaceName;
    int newStudentCount=0;
    vector<string>New;
    for(int i=0;i<ParticipantCount;i++){
        string name;
        double place;
        fin>>name>>place;
        PlaceName.push_back({place,name});
        if(id[name]==0){
            newStudentCount++;
            New.push_back(name);
        }
    }
    if(newStudentCount>0){
        for(int i=0;i<New.size();i++){
            cout<<New[i]<<"0 0"<<endl;
        }
        return 0;
    }
    else{

        sort(PlaceName.begin(),PlaceName.end());
        for(int i=0;i<ParticipantCount;i++){
            Participants[i].Place=PlaceName[i].first;
            Participants[i].Name=PlaceName[i].second;
        }


        vector<double>ExceptedPlace;
        //Find excepted place
        for(int i=0;i<ParticipantCount;i++){
            double res=1;
            for(int j=0;j<ParticipantCount;j++){
                if(i!=j){
                    res+=elo(st[id[Participants[i].Name]].CalculateRating,st[id[Participants[j].Name]].CalculateRating);
                }
            }
            ExceptedPlace.push_back(res);
        }
        

        //Avarage Place
        vector<double>AvagarePlace;
        for(int i=0;i<ParticipantCount;i++){
            double avarage=sqrt(Participants[i].Place*ExceptedPlace[i]);
            AvagarePlace.push_back(avarage);
        }


        //Excepted Rating
        auto FindRating=[&](double place) -> double {
            int l=1;
            int r=8000;
            while(l<r){
                int mid=(l+r)/2;
                double res=0.5;
                for(auto i=0;i<ParticipantCount;i++){
                    res+=elo(mid,st[id[Participants[i].Name]].CalculateRating);
                }
                if(res<place){
                    r=mid;
                }
                else{
                    l=mid+1;
                }
            }
            return l;
        };

        
        //Rating Change
        double Delta=0;
        vector<double>ParticipantDelta;
        for(int i=0;i<ParticipantCount;i++){
            double ExceptedRating=FindRating(AvagarePlace[i]);
            Delta+=(ExceptedRating-st[id[Participants[i].Name]].Rating)/2;
            ParticipantDelta.push_back((ExceptedRating-st[id[Participants[i].Name]].Rating)/2);
        }

        //Odoogoor ashiglagdahgui
        //double ChangeInDelta=-Delta/ParticipantCount;
        //fout<<ChangeInDelta<<endl;

        int MaxLengthName=50;
        int MaxLengthRating=7;
        int MaxChangeRating=6;
        for(int i=0;i<ParticipantCount;i++){
            int len=MaxLengthName-Participants[i].Name.size();
            
            //Odoogoor ashiglagdahgui
            //ParticipantDelta[i]+=ChangeInDelta;
            
            ParticipantDelta[i]=round(ParticipantDelta[i]);
            int NewRating=st[id[Participants[i].Name]].Rating+ParticipantDelta[i];
            fout<<Participants[i].Name;
            while(len--){
                fout<<" ";
            }
            string del="+0";
            int DeltaInt=ParticipantDelta[i];
            if(DeltaInt>0){
                del="+"+to_string(DeltaInt);
            }
            if(DeltaInt<0){
                del=to_string(DeltaInt);
            }
            fout<<st[id[Participants[i].Name]].Rating;
            int space=MaxLengthRating-to_string(st[id[Participants[i].Name]].Rating).size();
            while(space--){
                fout<<" ";
            }

            fout<<del;
            space=MaxChangeRating-del.size();

            while(space--){
                fout<<" ";
            }

            fout<<NewRating<<endl;


            /*
            space=MaxLengthRating-to_string(NewRating).size();
            while(space--){
                fout<<" ";
            }
            fout<<endl;
            */

            //Change in Rating,ContestCount
            int Id=id[Participants[i].Name];
            st[Id].Rating=NewRating;
            st[Id].ContestCount++;
        }

        fout<<endl<<endl;

        for(int i=1;i<=StudentCount;i++){
            fout<<st[i].Name<<" "<<st[i].Rating<<" "<<st[i].ContestCount<<endl;
        }
    
    
    }
}
```
