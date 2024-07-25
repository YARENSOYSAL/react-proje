app.js:
import React, { Component } from 'react';
import Navbar from './components/Navbar';
import './App.css';
import Users from './components/Users';
import AddUser from './components/AddUser';
class App extends Component {
  render() {
    return (
      <div className="App">
        <h1 style={{ color: "red" }}>Merhaba React</h1>
        <h1 style={{ color: "red" }}>Projeme Hoşgeldin</h1>
        <h1>1+1={1 + 1}</h1>
        <h1>8*9={8 * 9}</h1>
        <h4 style={{ color: "blue" }}>Merhaba</h4>
        <Navbar title="Çalışanlar" />
        <hr /> 
        <AddUser/>

        <Users/>
      </div>
    );
  }
}

export default App;

user.js
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import UserConsumer from '../context';


class User extends Component {
  constructor(props) {
    super(props);
    this.state = {
      isVisible : true 
    };
  }
  onClickEvent =(e) => {
    console.log(this);
    this.setState({
     isVisible : !this.state.isVisible
  }
  )
  }

  onDeleteUser = (dispatch,e) => {
    const {id} = this.props;
    // Consumer dispatch
    dispatch ({type : "DELETE_USER",payload:id});

  }
  static defaultProps = {
    name: "bilgi yok",
    salary: "bilgi yok",
    department: "bilgi yok"
  };
  static propTypes = {
    name: PropTypes.string.isRequired,
    salary: PropTypes.string.isRequired,
    department: PropTypes.string.isRequired,
    
  };
  render() {
    // destructuring
    const { name, department, salary } = this.props;
    const {isVisible} = this.state
    return (<UserConsumer>
      {
        value => {
          const {dispatch} = value;
          return (
            <div className='col-md-8 mb-4'>
            <div className='card'> 
              <div className='card-hearder d-flex justify-content-between'>
                <h4 className='d-inline'onClick={this.onClickEvent}>{name}</h4>
                <i onClick={this.onDeleteUser.bind(this,dispatch)} className='far fa-trash-alt' style={{cursor : "pointer"}}></i>
              </div>
              <hr/>
              {
                isVisible ? (<div className='card-body'>
                <p className='card-text'> maaş : {salary} </p>
                <p className='card-text'> departman : {department} </p>
                </div>) : null
              }
            </div>
            </div>
          );
        }
      }

    </UserConsumer>)
  }
}

export default User;

users.js
import React, { Component } from 'react';
import User from './User';
import UserConsumer from '../context';

class Users extends Component {
  render() {
    return (
      <UserConsumer>
        {
          value => {
            if (!value.users) {
              return <div>Loading...</div>;
            }

            const { users } = value;
            return (
              <div>
                {
                  users.map(user => (
                    <User
                      key={user.id}
                      id={user.id}
                      name={user.name}
                      salary={user.salary}
                      department={user.department}
                    />
                  ))
                }
              </div>
            );
          }
        }
      </UserConsumer>
    );
  }
}

export default Users;

navbar.js
import React from 'react'
import propTypes from "prop-types";


 function Navbar(props) {
  return (
    <div>
      <h3> {props.title} </h3>
    </div>
  )
}
Navbar.propTypes ={

    title : propTypes.string.isRequired
}
 Navbar.defaultProps = {
    title : "Default App"
 }
export default Navbar;

adduser.js
import React, { Component } from 'react'

 class AddUser extends Component {
  render() {
    return (
      <div className='col-md-8 mb-4'>
    <div className='card'>
    <div className='card-header'>
        <h4>kişi ekle</h4>
    </div>
    <div className='card-body'>
        <form>
            <div className='from-group'>
            <label htmlFor='name'> name </label>
            <input 
            taype ="text"
            name = 'name'
            id='id'
            placeholder='isim girin'
            class = 'from-control'
            />
            </div>
            
        </form>
        <form>
            <div className='from-group'>
            <label htmlFor='department'> department </label>
            <input 
            taype ="text"
            name = 'department'
            id='department'
            placeholder='departman giriniz'
            class = 'from-control'
            />
            </div>
            
        </form>
        <form>
            <div className='from-group'>
            <label htmlFor='salary'> salary </label>
            <input 
            taype ="text"
            name = 'salary'
            id='salary'
            placeholder='maaş girin'
            class = 'from-control'
            />
            </div>
            <button class ="btn btn-danger btn-block" type   = "submit"> kişiyi ekle </button>
            
        </form>
    </div>
    </div>
      </div>
    )
  }
}
export default AddUser;

context.js
import React, { Component } from 'react' 
import User from './components/User';

 const UserContext  = React.createContext();
 // provider , consumer
 const reducer =(state,action) =>{
  switch(action.type){
    case "DELETE_USER":
      return{
        ...state,
        users: state.users.filter(user => action.payload !== user.id)
      }
      default: 
      return state
  }
 }

 export class UserProvider extends Component {
    state = {
        users: [                    
          {
            id: 1,
            name: "Yaren Soysal",
            salary: "400.000",
            department: "Arge"
          },
          {
            id: 2,
            name: "Mahmut Soysal",
            salary: "700.000",
            department: "Kuaför"
          },
          {
            id: 3,
            name: "Furkan Taşdelen",
            salary: "100.000",
            department: "Bilişim"
          },
          {
            id:4,
            name: "behice soysal",
            salary: "800.000",
            department:"yönetici"
          }
        ],
        dispatch : action => {
          this.setState(state => reducer(state,action))
        }

      };
    
  render() {
    return (
      <UserContext.Provider value={this.state}>
        {this.props.children}
      </UserContext.Provider>
    )
  }
}
const UserConsumer =UserContext.Consumer;
export default UserConsumer;

